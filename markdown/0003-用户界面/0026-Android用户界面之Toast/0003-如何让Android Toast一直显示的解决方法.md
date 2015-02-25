Toast是Android用来显示显示信息的一种机制，和Dialog不一样的是，Toast是没有焦点的，而且Toast显示的时间有限，过一定的时间就会自动消失。想让Toast一直显示，怎么做呢？
Toast有个setDuration方法设置显示的。但很奇怪的只能设置两个值，Toast.LENGTH_LONG或Toast.LENGTH_SHORT。
设置其他值都没你想要的效果，只能是Toast.LENGTH_LONG或Toast.LENGTH_SHORT其中一值。
其实可以用Timer来解决。
```  
isRunning = true;  
timer = new Timer();  
timer.schedule(new TimerTask(){
    @Override
    public void run() {  
    // TODO Auto-generated method stub  
        while(isRunning){
            toast.show();
        }  
    }                         
}, 10);  
```