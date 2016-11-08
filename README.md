# 高通量测序数据分析实践操作  

## 操作说明    


**说明：`$`后为命令，其他的为输出或说明。**  

```
加载环境变量
$ module add bioinfo
```
## 实验设计  
比较野生型与突变体之间的基因表达差异，一组为野生型，一组为突变体，分别设了3次重复。  

序列文件 | 类型 | 样本编号
------- | ----- | ------
WLD-1.R1.fastq.gz, WLD-1.R2.fastq.gz | 野生型 | Wild-1
WLD-2.R1.fastq.gz, WLD-2.R2.fastq.gz | 野生型 | Wild-2
WLD-3.R1.fastq.gz, WLD-3.R2.fastq.gz | 野生型 | Wild-3
MUT-1.R1.fastq.gz, MUT-1.R2.fastq.gz | 突变体 | Mutant-1
MUT-2.R1.fastq.gz, MUT-2.R2.fastq.gz | 突变体 | Mutant-2
MUT-3.R1.fastq.gz, MUT-3.R2.fastq.gz | 突变体 | Mutant-3

## 一、数据前处理  

**1. 获取数据**
```
试验数据服务器存放地址
/bs1/data/NGS/data/
测序原始数据
/bs1/data/NGS/data/fq/
参考基因组
/bs1/data/NGS/data/ref/

准备工作目录
$ mkdir work work/00.fq work/db
$ cd work/00.fq
$ ln -s /bs1/data/NGS/data/fq/*.gz ./
$ ls -l
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-1.R1.fastq.gz -> /bs1/data/NGS/data/fq/MUT-1.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-1.R2.fastq.gz -> /bs1/data/NGS/data/fq/MUT-1.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-2.R1.fastq.gz -> /bs1/data/NGS/data/fq/MUT-2.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-2.R2.fastq.gz -> /bs1/data/NGS/data/fq/MUT-2.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-3.R1.fastq.gz -> /bs1/data/NGS/data/fq/MUT-3.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 MUT-3.R2.fastq.gz -> /bs1/data/NGS/data/fq/MUT-3.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-1.R1.fastq.gz -> /bs1/data/NGS/data/fq/WLD-1.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-1.R2.fastq.gz -> /bs1/data/NGS/data/fq/WLD-1.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-2.R1.fastq.gz -> /bs1/data/NGS/data/fq/WLD-2.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-2.R2.fastq.gz -> /bs1/data/NGS/data/fq/WLD-2.R2.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-3.R1.fastq.gz -> /bs1/data/NGS/data/fq/WLD-3.R1.fastq.gz
lrwxrwxrwx. 1 public public 39 11月  8 09:00 WLD-3.R2.fastq.gz -> /bs1/data/NGS/data/fq/WLD-3.R2.fastq.gz

```




**2. 质控（QC）**

fastqc使用请参考，[http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/)  
[测序质量好的报告样本](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/good_sequence_short_fastqc.html)  
[测序质量差的报告样本](http://www.bioinformatics.bbsrc.ac.uk/projects/fastqc/bad_sequence_fastqc.html)  

```
$ cd 00.fq/
$ mkdir qc
$ fastqc -o qc/ *.fastq.gz
$ ll qc/
总用量 8752
-rw-rw-r--. 1 public public 334917 11月  8 09:05 MUT-1.R1_fastqc.html
-rw-rw-r--. 1 public public 381568 11月  8 09:05 MUT-1.R1_fastqc.zip
-rw-rw-r--. 1 public public 347082 11月  8 09:08 MUT-1.R2_fastqc.html
-rw-rw-r--. 1 public public 401704 11月  8 09:08 MUT-1.R2_fastqc.zip
-rw-rw-r--. 1 public public 313485 11月  8 09:11 MUT-2.R1_fastqc.html
-rw-rw-r--. 1 public public 393983 11月  8 09:11 MUT-2.R1_fastqc.zip
-rw-rw-r--. 1 public public 323387 11月  8 09:15 MUT-2.R2_fastqc.html
-rw-rw-r--. 1 public public 408992 11月  8 09:15 MUT-2.R2_fastqc.zip
-rw-rw-r--. 1 public public 335965 11月  8 09:19 MUT-3.R1_fastqc.html
-rw-rw-r--. 1 public public 430604 11月  8 09:19 MUT-3.R1_fastqc.zip
-rw-rw-r--. 1 public public 342367 11月  8 09:23 MUT-3.R2_fastqc.html
-rw-rw-r--. 1 public public 439796 11月  8 09:23 MUT-3.R2_fastqc.zip
-rw-rw-r--. 1 public public 335152 11月  8 09:26 WLD-1.R1_fastqc.html
-rw-rw-r--. 1 public public 381084 11月  8 09:26 WLD-1.R1_fastqc.zip
-rw-rw-r--. 1 public public 338596 11月  8 09:29 WLD-1.R2_fastqc.html
-rw-rw-r--. 1 public public 383675 11月  8 09:29 WLD-1.R2_fastqc.zip
-rw-rw-r--. 1 public public 314118 11月  8 09:33 WLD-2.R1_fastqc.html
-rw-rw-r--. 1 public public 398020 11月  8 09:33 WLD-2.R1_fastqc.zip
-rw-rw-r--. 1 public public 312072 11月  8 09:37 WLD-2.R2_fastqc.html
-rw-rw-r--. 1 public public 395426 11月  8 09:37 WLD-2.R2_fastqc.zip
-rw-rw-r--. 1 public public 308044 11月  8 09:41 WLD-3.R1_fastqc.html
-rw-rw-r--. 1 public public 385617 11月  8 09:41 WLD-3.R1_fastqc.zip
-rw-rw-r--. 1 public public 311030 11月  8 09:45 WLD-3.R2_fastqc.html
-rw-rw-r--. 1 public public 393542 11月  8 09:45 WLD-3.R2_fastqc.zip
```
结果文件存放在`qc/`子目录中。  
>fastqc后如不接任何参数，则是以交互式图形界面运行，需要WindowsX server的支持  


如果发现测序质量比较差，需要作进一步过滤处理的，可以用`trimmomatic`进行处理。  
[trimmomatic参考使用方法](http://www.usadellab.org/cms/?page=trimmomatic)  

## 二、基本分析  

RNA-Seq项目分析主要可分为3种应用情景  

1. 有参考基因组，需要预测新转录本（a）
2. 有参考基因组，不需要预测新转录本（b）
3. 无参考基因组（c）

![Image]
(https://static-content.springer.com/image/art%3A10.1186%2Fs13059-016-0881-8/MediaObjects/13059_2016_881_Fig2_HTML.gif)

[分析流程a：Tophat -> Cufflinks]()  
[分析流程b：Kallisto]()  
[分析流程c：Trinity -> RSEM]()  

## 三、高级分析  


## 报告内容要求  
1. 测序质量报告  
2. Mapping结果统计  
3. 


