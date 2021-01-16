---
title: 上传Zip文件不解压读取文件内容时ZipEntry的size为-1的问题
date: 2018-03-03 21:27:51
categories: 遇到的问题
tags:
- ZipEntry.getSize()=-1
- ZipEntry的size为-1
---

# 简介

这几天在做通过流下载zip文件以及上传zip文件不解压读取zip文件内容的功能，在读取zip文件内容的时候遇到了size为-1的情况，在此记录下遇到的情况、解决办法、以及未解决的问题。

<!-- more -->

# 示例

将上传和下载zip文件的功能做成了一个示例，放到了github上，链接：[export-import-zip-use-stream](https://github.com/dachengxi/export-import-zip-use-stream.git)，可以尝试运行下。

# 遇到的问题

通过流下载zip文件之后，再次导入该zip文件，不解压读取zip文件内容，发现ZipEntry的size()返回-1，如下图所示：

![上传Zip文件不解压读取文件内容时ZipEntry的size为-1的问题](size-1.png)

但是尝试使用系统自带的压缩软件压缩了一个zip文件，并上传读取，发现一切正常，size不为-1。使用zipinfo命令查看两个文件作为对比，如下：

![上传Zip文件不解压读取文件内容时ZipEntry的size为-1的问题](zipinfo.png)

可以看到上面文件是通过导出功能生成的，红框里缺少size。而下面的是系统压缩软件压缩的zip文件，红框里面带有size大小。故猜测可能是由于代码里生成ZipEntry的时候没有设置size，compressize，crc32等属性的原因。

# 读取zip文件时ZipEntry的size为-1解决办法

直接读取当前ZipEntry的流，直到为-1为止，代码如下：

```java
@PostMapping("import")
@ResponseBody
public void importZip(@RequestParam("file")MultipartFile file) throws IOException {

    ZipInputStream zipInputStream = new ZipInputStream(file.getInputStream());

    ZipEntry zipEntry;
    while ((zipEntry = zipInputStream.getNextEntry()) != null) {
        if (zipEntry.isDirectory()) {
            // do nothing
        }else {
            String name = zipEntry.getName();
            long size = zipEntry.getSize();
            // unknown size
            // ZipEntry的size可能为-1，表示未知
            // 通过上面的几种方式下载，就会产生这种情况
            if (size == -1) {
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
                while (true) {
                    int bytes = zipInputStream.read();
                    if (bytes == -1) break;
                    baos.write(bytes);
                }
                baos.close();
                System.out.println(String.format("Name:%s,Content:%s",name,new String(baos.toByteArray())));
            } else { // ZipEntry的size正常
                byte[] bytes = new byte[(int) zipEntry.getSize()];
                zipInputStream.read(bytes, 0, (int) zipEntry.getSize());
                System.out.println(String.format("Name:%s,Content:%s",name,new String(bytes)));
            }
        }

    }
    zipInputStream.closeEntry();
    zipInputStream.close();
}
```

此时可以正确读取文件内容。

# 下载文件时设置size的解决办法

需要设置ZipEntry的size，compresSize以及crc32等属性。