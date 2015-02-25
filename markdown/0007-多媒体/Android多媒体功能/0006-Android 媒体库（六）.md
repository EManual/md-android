org.xmlrpc.android
Base64Coder.java
```  
[Tags]/**
 [Tags]* A Base64 Encoder/Decoder.
 [Tags]* <p>
 [Tags]* This class is used to encode and decode data in Base64 format as described in
 [Tags]* RFC 1521.
 [Tags]* <p>
 [Tags]* This is "Open Source" software and released under the <a
 [Tags]* href="http://www.gnu.org/licenses/lgpl.html">GNU/LGPL</a> license.<br>
 [Tags]* It is provided "as is" without warranty of any kind.<br>
 [Tags]* Copyright 2003: Christian d'Heureuse, Inventec Informatik AG, Switzerland.<br>
 [Tags]* Home page: <a href="http://www.source-code.biz">www.source-code.biz</a><br>
 [Tags]* <p>
 [Tags]* Version history:<br>
 [Tags]* 2003-07-22 Christian d'Heureuse (chdh): Module created.<br>
 [Tags]* 2005-08-11 chdh: Lincense changed from GPL to LGPL.<br>
 [Tags]* 2006-11-21 chdh:<br>
 [Tags]* Method encode(String) renamed to encodeString(String).<br>
 [Tags]* Method decode(String) renamed to decodeString(String).<br>
 [Tags]* New method encode(byte[],int) added.<br>
 [Tags]* New method decode(String) added.<br>
 [Tags]*/
class Base64Coder {
	// Mapping table from 6-bit nibbles to Base64 characters.
	private static char[] map1 = new char[64];
	static {
		int i = 0;
		for (char c = 'A'; c <= 'Z'; c++) {
			map1[i++] = c;
		}
		for (char c = 'a'; c <= 'z'; c++) {
			map1[i++] = c;
		}
		for (char c = '0'; c <= '9'; c++) {
			map1[i++] = c;
		}
		map1[i++] = '+';
		map1[i++] = '/';
	}
	// Mapping table from Base64 characters to 6-bit nibbles.
	private static byte[] map2 = new byte[128];
	static {
		for (int i = 0; i < map2.length; i++) {
			map2[i] = -1;
		}
		for (int i = 0; i < 64; i++) {
			map2[map1[i]] = (byte) i;
		}
	}
	[Tags]/**
	 [Tags]* Encodes a string into Base64 format. No blanks or line breaks are
	 [Tags]* inserted.
	 [Tags]* @param s
	 [Tags]*            a String to be encoded.
	 [Tags]* @return A String with the Base64 encoded data.
	 [Tags]*/
	static String encodeString(String s) {
		return new String(encode(s.getBytes()));
	}
	[Tags]/**
	 [Tags]* Encodes a byte array into Base64 format. No blanks or line breaks are
	 [Tags]* inserted.
	 [Tags]* @param in
	 [Tags]*            an array containing the data bytes to be encoded.
	 [Tags]* @return A character array with the Base64 encoded data.
	 [Tags]*/
	static char[] encode(byte[] in) {
		return encode(in, in.length);
	}

	[Tags]/**
	 [Tags]* Encodes a byte array into Base64 format. No blanks or line breaks are
	 [Tags]* inserted.
	 [Tags]* @param in
	 [Tags]*            an array containing the data bytes to be encoded.
	 [Tags]* @param iLen
	 [Tags]*            number of bytes to process in <code>in</code>.
	 [Tags]* @return A character array with the Base64 encoded data.
	 [Tags]*/
	static char[] encode(byte[] in, int iLen) {
		int oDataLen = (iLen * 4 + 2) / 3; // output length without padding
		int oLen = ((iLen + 2) / 3) * 4; // output length including padding
		char[] out = new char[oLen];
		int ip = 0;
		int op = 0;
		while (ip < iLen) {
			int i0 = in[ip++] & 0xff;
			int i1 = ip < iLen ? in[ip++] & 0xff : 0;
			int i2 = ip < iLen ? in[ip++] & 0xff : 0;
			int o0 = i0 >>> 2;
			int o1 = ((i0 & 3) << 4) | (i1 >>> 4);
			int o2 = ((i1 & 0xf) << 2) | (i2 >>> 6);
			int o3 = i2 & 0x3F;
			out[op++] = map1[o0];
			out[op++] = map1[o1];
			out[op] = op < oDataLen ? map1[o2] : '=';
			op++;
			out[op] = op < oDataLen ? map1[o3] : '=';
			op++;
		}
		return out;
	}
	[Tags]/**
	 [Tags]* Decodes a string from Base64 format.
	 [Tags]* @param s
	 [Tags]*            a Base64 String to be decoded.
	 [Tags]* @return A String containing the decoded data.
	 [Tags]* @throws IllegalArgumentException
	 [Tags]*             if the input is not valid Base64 encoded data.
	 [Tags]*/
	static String decodeString(String s) {
		return new String(decode(s));
	}

	[Tags]/**
	 [Tags]* Decodes a byte array from Base64 format.
	 [Tags]* @param s
	 [Tags]*            a Base64 String to be decoded.
	 [Tags]* @return An array containing the decoded data bytes.
	 [Tags]* @throws IllegalArgumentException
	 [Tags]*             if the input is not valid Base64 encoded data.
	 [Tags]*/
	static byte[] decode(String s) {
		return decode(s.toCharArray());
	}
	[Tags]/**
	 [Tags]* Decodes a byte array from Base64 format. No blanks or line breaks are
	 [Tags]* allowed within the Base64 encoded data.
	 [Tags]* @param in
	 [Tags]*            a character array containing the Base64 encoded data.
	 [Tags]* @return An array containing the decoded data bytes.
	 [Tags]* @throws IllegalArgumentException
	 [Tags]*             if the input is not valid Base64 encoded data.
	 [Tags]*/
	static byte[] decode(char[] in) {
		int iLen = in.length;
		if (iLen % 4 != 0) {
			throw new IllegalArgumentException(
					"Length of Base64 encoded input string is not a multiple of 4.");
		}
		while (iLen > 0 && in[iLen - 1] == '=') {
			iLen--;
		}
		int oLen = (iLen * 3) / 4;
		byte[] out = new byte[oLen];
		int ip = 0;
		int op = 0;
		while (ip < iLen) {
			int i0 = in[ip++];
			int i1 = in[ip++];
			int i2 = ip < iLen ? in[ip++] : 'A';
			int i3 = ip < iLen ? in[ip++] : 'A';
			if (i0 > 127 || i1 > 127 || i2 > 127 || i3 > 127) {
				throw new IllegalArgumentException(
						"Illegal character in Base64 encoded data.");
			}
			int b0 = map2[i0];
			int b1 = map2[i1];
			int b2 = map2[i2];
			int b3 = map2[i3];
			if (b0 < 0 || b1 < 0 || b2 < 0 || b3 < 0) {
				throw new IllegalArgumentException(
						"Illegal character in Base64 encoded data.");
			}
			int o0 = (b0 << 2) | (b1 >>> 4);
			int o1 = ((b1 & 0xf) << 4) | (b2 >>> 2);
			int o2 = ((b2 & 3) << 6) | b3;
			out[op++] = (byte) o0;
			if (op < oLen) {
				out[op++] = (byte) o1;
			}
			if (op < oLen) {
				out[op++] = (byte) o2;
			}
		}
		return out;
	}
	// Dummy constructor.
	private Base64Coder() {
	}
}
```