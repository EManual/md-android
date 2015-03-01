今天就和大家一起学习一下排序的算法，别的就不多说了，代码给大家看看。
```  
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.List;
import java.util.Scanner;
public class Partition01 {
	public static int data[];
	public static void main(String[] args) {
		System.out.print("请输入数组元素的大小：");
		String str;
		Scanner in = new Scanner(System.in);
		int number = in.nextInt();
		data = new int[number];
		System.out.println("请输入" + number + "个数组元素(每输入一个元素后按回车)");
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		for (int i = 0; i < number; i++) {
			try {
				str = br.readLine();
				data[i] = Integer.parseInt(str);
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		System.out.println("=========数组排序前的元素=========");
		printInfo();
		QuickSort(0, number - 1);
		System.out.println();
		System.out.println("=========数组排序后的元素=========");
		printInfo();
	}
	// 取得基准位置
	@SuppressWarnings("unchecked")
	public static int getPivotpos(int low, int high) {
		int pivotpos = low;
		int pivot = data[low];
		for (int i = low + 1; i <= high; i++) { // 检测整个序列，进行划分
			if (data[i] < pivot) {
				pivotpos++;
				if (pivotpos != i) {
					swap(pivotpos, i); // 小于基准的交换到左侧去
				}
			}
		}
		data[low] = data[pivotpos];
		data[pivotpos] = pivot; // 将基准元素就位
		return pivotpos; // 返回基准元素位置
	}
	/*
	  * 对数组元素进行排序，使各元素按排序码非递减有序，把参加排序的数组元素分成两部分，排在 它左边的元素的排序码都小于它，而右边的都大于或等于它.
	  */
	public static void QuickSort(int left, int right) {
		if (left < right) { // 元素序列长度大于1时
			int pivotpos = getPivotpos(left, right); // 划分
			QuickSort(left, pivotpos - 1); // 对左侧的元素施行同样的处理
			QuickSort(pivotpos + 1, right); // 对右侧的元素施行同样的处理
		}
	}
	// 交换数据
	public static void swap(int i, int j) {
		int temp = data[i];
		data[i] = data[j];
		data[j] = temp;
	}
	// 输出显示数组元素
	public static void printInfo() {
		for (int i = 0; i < data.length; i++) {
			System.out.print(data[i] + "\t");
		}
	}
}
```