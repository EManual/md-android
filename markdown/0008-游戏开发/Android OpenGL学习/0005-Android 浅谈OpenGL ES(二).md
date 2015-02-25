封装的三角形的类 
```  
import java.nio.ByteBuffer;
import java.nio.ByteOrder;
import java.nio.FloatBuffer;
import java.nio.ShortBuffer;
import java.util.Random;
public class Triangle {
	private short[] myIndecesArray = { 0, 1, 2 };
	private int numberOfVertices = 3;
	private float red = 0f;
	private float green = 0f;
	private float blue = 0f;
	// a raw buffer to hold indices allowing a reuse of points.
	private ShortBuffer indexBuffer;
	// a raw buffer to hold the vertices
	private FloatBuffer vertexBuffer;
	private Random random = new Random();
	public Triangle() {
		red = random.nextFloat();
		red = red - (float) Math.floor(red);
		green = random.nextFloat();
		green = green - (float) Math.floor(green);
		blue = random.nextFloat();
		blue = blue - (float) Math.floor(blue);
		ByteBuffer vbb = ByteBuffer.allocateDirect(numberOfVertices * 3 * 4);
		vbb.order(ByteOrder.nativeOrder());
		vertexBuffer = vbb.asFloatBuffer();
		ByteBuffer ibb = ByteBuffer.allocateDirect(numberOfVertices * 2);
		ibb.order(ByteOrder.nativeOrder());
		indexBuffer = ibb.asShortBuffer();
		float[] coords = { randomValue(red), randomValue(red),
				randomValue(red), randomValue(green), randomValue(green),
				randomValue(green), randomValue(blue), randomValue(blue),
				randomValue(blue) };
		for (int i = 0; i < numberOfVertices; i++) {
			for (int j = 0; j < 3; j++) {
				vertexBuffer.put(coords[i * 3 + j]);
			}
		}
		for (int i = 0; i < 3; i++) {
			indexBuffer.put(myIndecesArray[i]);
		}
		vertexBuffer.position(0);
		indexBuffer.position(0);
	}
	private float randomValue(float value) {
		int tmp = random.nextInt(2);
		if (tmp == 0) {
			tmp = -1;
		}
		return value * tmp;
	}
	public FloatBuffer getVertexBuffer() {
		return vertexBuffer;
	}
	public ShortBuffer getIndexBuffer() {
		return indexBuffer;
	}
	public int getNumberOfVertices() {
		return numberOfVertices;
	}
	public float getGreen() {
		return green;
	}
	public float getRed() {
		return red;
	}
	public float getBlue() {
		return blue;
	}
}
```