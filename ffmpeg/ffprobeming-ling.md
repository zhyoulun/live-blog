# ffprobe命令

显示支持的格式

```
ffprobe -formats

输出结果
File formats:
 D. = Demuxing supported
 .E = Muxing supported
 --
 D  3dostr          3DO STR
  E 3g2             3GP2 (3GPP2 file format)
  E 3gp             3GP (3GPP file format)
```

显示可用的demuxers

```
ffprobe -demuxers

输出结果
File formats:
 D. = Demuxing supported
 .E = Muxing supported
 --
 D  3dostr          3DO STR
 D  4xm             4X Technologies
 D  aa              Audible AA format files
 D  aac             raw ADTS AAC (Advanced Audio Coding)
```

显示可用的muxers

```
ffprobe -muxers

输出结果
File formats:
 D. = Demuxing supported
 .E = Muxing supported
 --
  E 3g2             3GP2 (3GPP2 file format)
  E 3gp             3GP (3GPP file format)
  E a64             a64 - video for Commodore 64
```



