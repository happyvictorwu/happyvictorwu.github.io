---
title: Java快速读写模板
date: 2020-02-19 22:01:11
- [Java, 集合类]
- [算法, 技巧]
tags:
- 编程
---



## 刷题快速读写做题模板1
```java
import java.io.OutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.PrintWriter;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.io.BufferedReader;

public class Main {
    static class InputReader {
        public BufferedReader reader;
        public StringTokenizer tokenizer;

        public InputReader(InputStream stream) {
            reader = new BufferedReader(new InputStreamReader(stream), 32768);
            tokenizer = null;
        }

        public String nextLine() {
            while(tokenizer == null || !tokenizer.hasMoreElements()){
                try {
                    tokenizer =new StringTokenizer(reader.readLine());
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            return tokenizer.nextToken("\n");
        }

        public String next() {
            while (tokenizer == null || !tokenizer.hasMoreTokens()) {
                try {
                    tokenizer = new StringTokenizer(reader.readLine());
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }
            return tokenizer.nextToken();
        }

        public int nextInt() {
            return Integer.parseInt(next());
        }

        public long nextLong() {
            return Long.parseLong(next());
        }

    }
    public static void main(String[] args) {
        InputStream inputStream = System.in;
        OutputStream outputStream = System.out;
        InputReader in = new InputReader(inputStream);
        PrintWriter out = new PrintWriter(outputStream);
        Solver solver = new Solver();
        int T = 1;
        // int T = in.nextInt();
        for (int i = 1; i <= T; i++) solver.start(T, in, out);
        out.close();
    }

    static class Solver {
        public void start(int testNumber, InputReader in, PrintWriter out) {
            
        }
    }
}
```

## 刷题快速读写做题模板2
```java
import java.io.*;
import java.util.InputMismatchException;

public class Main {

    static class Solver {
        public void doing(int testNumber, InputReader in, OutputWriter out) {
            
        }
    }

    public static void main(String[] args) throws FileNotFoundException {
//        FileInputStream fis = new FileInputStream("out/production/interview/in.txt");
//        System.setIn(fis);
        InputReader in = new InputReader(System.in);
        OutputWriter out = new OutputWriter(System.out);
        Solver solver = new Solver();
//        int testCount = Integer.parseInt(in.next());    // many case
        int testCount = 1;    // one case
        for (int i = 1; i <= testCount; i++) solver.doing(i, in, out);
        out.close();
    }

    // ----- IO -----
    static class InputReader {
        private InputStream stream;
        private byte[] buf = new byte[1 << 16];
        private int curChar;
        private int numChars;

        public InputReader(InputStream stream) {
            this.stream = stream;
        }

        public int read() {
            if (this.numChars == -1) {
                throw new InputMismatchException();
            } else {
                if (this.curChar >= this.numChars) {
                    this.curChar = 0;

                    try {
                        this.numChars = this.stream.read(this.buf);
                    } catch (IOException var2) {
                        throw new InputMismatchException();
                    }

                    if (this.numChars <= 0) {
                        return -1;
                    }
                }

                return this.buf[this.curChar++];
            }
        }

        public int nextInt() {
            int c;
            for (c = this.read(); isSpaceChar(c); c = this.read()) {
                ;
            }

            byte sgn = 1;
            if (c == 45) {
                sgn = -1;
                c = this.read();
            }

            int res = 0;

            while (c >= 48 && c <= 57) {
                res *= 10;
                res += c - 48;
                c = this.read();
                if (isSpaceChar(c)) {
                    return res * sgn;
                }
            }

            throw new InputMismatchException();
        }

        public String next() {
            int c;
            while (isSpaceChar(c = this.read())) {
                ;
            }

            StringBuilder result = new StringBuilder();
            result.appendCodePoint(c);

            while (!isSpaceChar(c = this.read())) {
                result.appendCodePoint(c);
            }

            return result.toString();
        }

//        long nextLong()
//        {
//            return Long.parseLong(next());
//        }
//        double nextDouble()
//        {
//            return Double.parseDouble(next());
//        }
//        BigInteger nextBigInteger()
//        {
//            return new BigInteger(next());
//        }
//        BigDecimal nextBigDecimal()
//        {
//            return new BigDecimal(next());
//        }

        public static boolean isSpaceChar(int c) {
            return c == 32 || c == 10 || c == 13 || c == 9 || c == -1;
        }

    }
    static class OutputWriter {
        private final PrintWriter writer;

        public OutputWriter(OutputStream outputStream) {
            writer = new PrintWriter(new BufferedWriter(new OutputStreamWriter(outputStream)));
        }

        public OutputWriter(Writer writer) {
            this.writer = new PrintWriter(writer);
        }

        public void printf(String format, Object... objects) {
            writer.printf(format, objects);
        }

        public void close() {
            writer.close();
        }

    }
}
```

`Integer.parseInt(in.next())`

## IO
将System.out的输入（出）重定向到文件输入（出）
```java
// 输入
FileInputStream fis = new FileInputStream("out/production/interview/in.txt");
System.setIn(fis);

// 输出
PrintStream ps = new PrintStream(new FileOutputStream("output.txt"));
System.setOut(ps);

Scanner in = new Scanner(new BufferedReader(new InputStreamReader(System.in)));
```


