---
title: Base 64
date: 2018-07-23 14:06:41
tags: Java
---

# Base64

# 转换步骤

准备自己写一个base64 encode和decode的方法，首先说一下转换步骤，先以英文为例：

1，三个字符一组，获取字母的Ascii码。

2，把Ascii转成二进制，将得到的二进制连在一起，一共24位，分成6位一组（之前是8位一组），并且在前面加两个0。最后不足8位的在后面补0。

3，把新的二进制数字转成十进制，根据 base64对应的转换表得到对应的字符。

# 关键代码

以下是关键代码，并不是完整代码

1，获取Ascii码

    String s="asdfdfdfdsfafdgsdfasf123435";
    char[] cs=s.toCharArray();
    for(int i:cs){
    	System.out.println(i);
    }

2，十进制数字转二进制

    Integer.toBinaryString(int i)

3，截取前6位

    substring(0,6);

4，二进制转十进制

    Integer.valueOf("0101",2).toString()

5，根据以下对照表转换

    	 0　A　　17　R　　　34　i　　　51　z
    
    　　1　B　　18　S　　　35　j　　　52　0
    
    　　2　C　　19　T　　　36　k　　　53　1
    
    　　3　D　　20　U　　　37　l　　　54　2
    
    　　4　E　　21　V　　　38　m　　　55　3
    
    　　5　F　　22　W　　　39　n　　　56　4
    
    　　6　G　　23　X　　　40　o　　　57　5
    
    　　7　H　　24　Y　　　41　p　　　58　6
    
    　　8　I　　　25　Z　　　42　q　　　59　7
    
    　　9　J　　26　a　　　43　r　　　60　8
    
    　　10　K　　27　b　　　44　s　　　61　9
    
    　　11　L　　28　c　　　45　t　　　62　+
    
    　　12　M　　29　d　　　46　u　　　63　/
    
    　　13　N　　30　e　　　47　v
    
    　　14　O　　31　f　　　48　w　　　
    
    　　15　P　　32　g　　　49　x
    
    　　16　Q　　33　h　　　50　y

    private static final char[] toBase64 = {
                'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M',
                'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z',
                'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm',
                'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z',
                '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', '/'
            };

具体代码：

    import java.util.ArrayList;
    
    public class Base64 {
    
    	public static String DECODE = "decode";
    	public static String ENCODE = "encode";
    
    	/**
    	 * 字符串长度
    	 */
    	private static int len;
    
    	public String code(String str, String type) {
    		len = str.length();
    		getBinary(str);
    		if (type == DECODE) {
    			return deCode();
    		} else if (type == ENCODE) {
    			return enCode();
    		}
    		return "error";
    	}
    
    	private String enCode() {
    
    		return "";
    	}
    
    	private String deCode() {
    
    		return "";
    	}
    
    	private ArrayList binary = new ArrayList();
    
    	/**
    	 * 获取Ascii转换后的二进制List
    	 * 
    	 * @param s
    	 * @return
    	 */
    	private ArrayList getBinary(String s) {
    
    		s = "asdfdfdfdsfafdgsdfasf123435";
    		char[] cs = s.toCharArray();
    		for (int i : cs) {
    			binary.add(d2b(i));
    			System.out.println(i);
    		}
    		return binary;
    	}
    
    	/**
    	 * 十进制转二进制
    	 * 
    	 * @return
    	 */
    	private String d2b(int i) {
    		return Integer.toBinaryString(i);
    	}
    
    	/**
    	 * 二进制转十进制
    	 * 
    	 * @return
    	 */
    	private String b2d(String s) {
    		return Integer.valueOf(String.valueOf(s), 2).toString();
    	}
    
    	/**
    	 * 拼接字符串
    	 */
    	private String appendString(ArrayList list) {
    		StringBuilder sb = new StringBuilder();
    
    		for (Object o : list) {
    			sb.append(o.toString());
    		}
    		return sb.toString();
    
    	}
    
    	/**
    	 * 截取字符串
    	 * 
    	 * @param s
    	 * @param begin
    	 * @param end
    	 */
    	private String subString(String s, int begin, int end) {
    		return s.substring(begin, end);
    	}
    
    	private ArrayList<String> aList = new ArrayList<>();
    	/**
    	 * 把获取到的二进制数据截取成6位一组，并转成十进制
    	 */
    	private void c() {
    		String binaryString;
    		char c;
    		String s;
    		binaryString = appendString(binary);
    		for (int i = 6; i < binaryString.length() - 6; i += 6) {
    			c = toBase64[Integer.valueOf(b2d(subString(binaryString, i-6, i)))];
    			s = String.valueOf(c);
    			aList.add(s);
    		}
    	}
    	
    	private static final char[] toBase64 = {
                'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M',
                'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z',
                'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm',
                'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z',
                '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', '/'
            };
    }