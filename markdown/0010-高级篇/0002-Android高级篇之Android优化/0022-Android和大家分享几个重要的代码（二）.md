#### 4 硬件调用
vibratorOn、vibratorOff对应的jni代码如下：
文件：frameworks/base/services/jni/com_android_server_HardwareService.cpp
```  
static void vibratorOn(JNIEnv *env, jobject clazz, jlong timeout_ms)
{
	// LOGI("vibratorOn\n");
	vibrator_on(timeout_ms);
}
static void vibratorOff(JNIEnv *env, jobject clazz)
{
	// LOGI("vibratorOff\n");
	vibrator_off();
}
```
vibrator_on、vibrator_off 接口的提供者为如下硬件原型。
#### 
#### 5, 硬件原型
文件：hardware/libhardware_legacy/vibrator/vibrator.c
```  
define THE_DEVICE "/sys/class/timed_output/vibrator/enable
```
```  
static int sendit(int timeout_ms)
{
	int nwr, ret, fd;
	char value[20];
	ifdef QEMU_HARDWARE
	if (qemu_check()) {
		return qemu_control_command("vibrator:%d", timeout_ms );
	}
	endif
	fd = open(THE_DEVICE, O_RDWR);
	if(fd < 0)
		return errno;
	nwr = sprintf(value, "%d\n", timeout_ms);
	ret = write(fd, value, nwr);
	close(fd);
	return (ret == nwr) ? 0 : -1;
}
int vibrator_on(int timeout_ms)
{
	/* constant on, up to maximum allowed time */
	return sendit(timeout_ms);
}
int vibrator_off()
{
	return sendit(0);
}
```
由以上代码可知，开启振动时是往文件/sys/class/timed_output/vibrator/enable写入振动的时间长度；关闭振动时，其时间长度为0./sys/class/timed_output/vibrator/enable 的真实路径根据实际作修改。
#### 6，驱动代码　
创建timed_output类
kernel\drivers\staging\android\Timed_output.c
在sys/class目录创建timed_output子目录和文件enable
```  
timed_output_class = class_create（THIS_MODULE， "timed_output"）；
```
创建timed_output子目录
```  
ret = device_create_file（tdev->dev， &dev_attr_enable）；
```
在sys/class/timed_output子目录创建文件enable
```  
static int create_timed_output_class(void)
{
	if (!timed_output_class) {
		timed_output_class = class_create(THIS_MODULE, "timed_output");
		if (IS_ERR(timed_output_class))
		return PTR_ERR(timed_output_class);
		atomic_set(&device_count, 0);
	}
	return 0;
}
int timed_output_dev_register(struct timed_output_dev *tdev)
{
	int ret;
	if (!tdev || !tdev->name || !tdev->enable || !tdev->get_time)
		return -EINVAL;
	ret = create_timed_output_class();
	if (ret < 0)
		return ret;
	tdev->index = atomic_inc_return(&device_count);
	tdev->dev = device_create(timed_output_class, NULL,
	MKDEV(0, tdev->index), NULL, tdev->name);
	if (IS_ERR(tdev->dev))
		return PTR_ERR(tdev->dev);
	ret = device_create_file(tdev->dev, &dev_attr_enable);
	if (ret < 0)
		goto err_create_file;
	dev_set_drvdata(tdev->dev, tdev);
	tdev->state = 0;
	return 0;
	err_create_file:
	device_destroy(timed_output_class, MKDEV(0, tdev->index));
	printk(KERN_ERR "timed_output: Failed to register driver %s\n", tdev->name);
	return ret;
}
EXPORT_SYMBOL_GPL(timed_output_dev_register);
```
驱动注册马达的驱动，注册一个定时器用于控制震动时间（回调函数vibrator_timer_func），注册两个队列，一共给马达打开用，一共为马达震动关闭用。
```  
static void pmic_vibrator_on(struct work_struct *work)
{
	set_pmic_vibrator(1);
}
static void pmic_vibrator_off(struct work_struct *work)
{
	set_pmic_vibrator(0);
}
static void timed_vibrator_on(struct timed_output_dev *sdev)
{
	schedule_work(&work_vibrator_on);
}
static void timed_vibrator_off(struct timed_output_dev *sdev)
{
	schedule_work(&work_vibrator_off);
}
static void vibrator_enable(struct timed_output_dev *dev, int value)
{
	hrtimer_cancel(&vibe_timer);
	if (value == 0)
		timed_vibrator_off(dev);
	else {
		value = (value > 15000 ? 15000 : value);
		timed_vibrator_on(dev);
		hrtimer_start(&vibe_timer,
		ktime_set(value / 1000, (value % 1000) * 1000000),
		HRTIMER_MODE_REL);
	}
}
static int vibrator_get_time(struct timed_output_dev *dev)
{
	if (hrtimer_active(&vibe_timer)) {
		ktime_t r = hrtimer_get_remaining(&vibe_timer);
		return r.tv.sec * 1000 + r.tv.nsec / 1000000;
	} else
		return 0;
}
static enum hrtimer_restart vibrator_timer_func(struct hrtimer *timer)
{
	timed_vibrator_off(NULL);
	return HRTIMER_NORESTART;
}
static struct timed_output_dev pmic_vibrator = {
	.name = "vibrator",
	.get_time = vibrator_get_time,
	.enable = vibrator_enable,
};
void __init pxa_init_pmic_vibrator(void)
{
	INIT_WORK(&work_vibrator_on, pmic_vibrator_on);
	INIT_WORK(&work_vibrator_off, pmic_vibrator_off);
	hrtimer_init(&vibe_timer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);
	vibe_timer.function = vibrator_timer_func;
	timed_output_dev_register(&pmic_vibrator);
}
```
当上层要设置马达震动时，往文件/sys/class/timed_output/vibrator/enable写入振动的时间长度，通过
```  
static ssize_t enable_store(struct device *dev, struct device_attribute *attr, const char *buf, size_t size)
{
	struct timed_output_dev *tdev = dev_get_drvdata(dev);
	int value;
	sscanf(buf, "%d", &value);
	tdev->enable(tdev, value);
	return size;
}
```
调用驱动的enable函数也就是vibrator_enable（ .enable = vibrator_enable）
```  
vibrator_enable
|
|
v
timed_vibrator_on(dev);
|
|
v
schedule_work(&work_vibrator_on);
|
|
v
pmic_vibrator_on
|
|
v
set_pmic_vibrator(1); //给马达供电震动
|
|
v
hrtimer_start(&vibe_timer,
ktime_set(value / 1000, (value % 1000) * 1000000),
HRTIMER_MODE_REL);
```
最终是设置马达的硬件控制驱动管给马达供电，并且启动定时器，定时时间是上层给的参数。
定时时间到了就调用定时器的回调函数vibrator_timer_func
```  
vibrator_timer_func
|
|
v
timed_vibrator_off(NULL);
|
|
v
schedule_work(&work_vibrator_off);
|
|
v
pmic_vibrator_off
|
|
v
set_pmic_vibrator(0); //断开马达的供电，马达停止震动
```
最终是设置马达的硬件控制驱动管断开马达供电，停止马达震动。