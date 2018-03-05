---
title: java之char数组输出
date: 2015-02-07 10:37:46
tags: [java,基础]
category: java基础
---
首先println方法对数组的处理是先转化为String类型，我们可以进入println源码看一下，我是创建了一个String数组进去看了下

```Java
    public void println(Object x) {
        String s = String.valueOf(x);
        synchronized (this) {
            print(s);
            newLine();
        }
    }
```

首先他都转化成了String，然后进入print方法

```Java
    public void print(String s) {
        if (s == null) {
            s = "null";
        }
        write(s);
    }

```

接下来我们再进入write方法

```Java
    private void write(String s) {
        try {
            synchronized (this) {
                ensureOpen();//确认输出流是否打开了
                textOut.write(s);//就是一个bufferWriter，写入到缓冲区
                textOut.flushBuffer();//刷新缓存区
                charOut.flushBuffer();//OutputStreamWriter
                if (autoFlush && (s.indexOf('\n') >= 0))
                    out.flush();//这里就是与char数组的却别，他直接把String s刷新到了控制台的输出流中，但是记住他没有和toString方法相关联
            }
        }
        catch (InterruptedIOException x) {
            Thread.currentThread().interrupt();
        }
        catch (IOException x) {
            trouble = true;
        }
    }
```

那我们看看char数组的write方法

```Java
    private void write(char buf[]) {
        try {
            synchronized (this) {
                ensureOpen();
                textOut.write(buf);
                textOut.flushBuffer();
                charOut.flushBuffer();
                if (autoFlush) {//我们可以看到，他是把每个字符都写入到了控制台的输出流了。
                    for (int i = 0; i < buf.length; i++)
                        if (buf[i] == '\n')
                            out.flush();
                }
            }
        }
        catch (InterruptedIOException x) {
            Thread.currentThread().interrupt();
        }
        catch (IOException x) {
            trouble = true;
        }
    }
```

其实这是第二次遇到这问题了，但是之前没有非常明白，这里弄清楚记录一下，也供大家参考一下