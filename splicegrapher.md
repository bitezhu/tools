---
title: splicegrapher 
tags: tutorial
grammar_cjkRuby: true
---



>The average human protein coding transcript has ~8-10 exons and is ~2,000 bp in length.

1、将基因组序列和model文件拷到当前目录，或者配置SpliceGrapher.cfg文件

```
build_classifiers.py -d gt,gc -n 400 （为了节省时间。-n 可适当调高，默认2000 ）
grep roc *.cfg
```

> Scores typically will be above 0.90
生成好的分类器classifiers.zip，/data/BIN/zhush/bin/deepTools2.0/SpliceGrapher-0.2.4/classifiers/有很多已知物种的分类器,不用重复生成

2、filter the alignment output,需要第一步生成的分类器和比对sam文件
```
sam_filter.py alignments.sam.gz classifiers.zip -o filtered.sam.gz -z
```

3、Predict a Splice Graph （提供基因model名称）
```
predict_splicegraph.py AT2G04700(intersted gene) -d filtered.sam.gz -o AT2G04700.gff -m reference.gtf
```

4、可视化（配置一份AT2G04700_plot.cfg，下面有例子，高级配置可参考SpliceGrapher/plot/PlotterConfig.py）
```
plotter.py AT2G04700_plot.cfg
```

>配置文件：
深度信息文件filtered.depths 先用sam_to_depths.py 得到，这样画图会快很多
** 标注的文件每次画图记得改
[SinglePlotConfig]
legend = True
output_file = human_graph.pdf
height = 12.0
width = 16.0
fontsize = 16
[GeneModelGraph]
plot_type = gene
file_format = gene_model
gene_name = ENSG00000188976 ******
source_file = reference.gtf
title_string = TAIR10 Gene Model for AT2G04700
[SpliceGrapher]
plot_type = splice_graph
source_file = ENSG00000227232.gff *******
title_string = SpliceGrapher AT2G04700 Prediction
[isoforms]
plot_type = isoforms
source_file = ENSG00000227232.gff *******
title_string = isoforms Model for ENSG00000160072
[Junctions]
plot_type = junctions
labels = True
min_coverage = 2
source_file = filtered.depths
title_string = Spliced Reads
[Coverage]
plot_type = read_depth
source_file = filtered.depths
log_threshold = 1000
title_string = Read Coverage

### 实例结果（人RNA数据）：

* py /data/BIN/zhush/bin/deepTools2.0/SpliceGrapher-0.2.4/renewGeneID4stringtie.py /data/project/NCRNA/HN16020320/result/3.merged_asm/merged.fmt.gtf
* sam_filter.py Aligned.sortedByCoord.out.bam Homo_sapiens.zip -f Homo_sapiens.GRCh38.dna_sm.toplevel.filter.fa -m newattr.gtf -v -o filtered.1.sam
* （gtf model 文件最后一列需要有transcript_id属性，即可，去掉第三列为gene的行，可以直接用cufflinks或者stringtie的输出。比对输入可以为bam格式文件，输出只能是sam格式）

* sam_split.py 分割sam （输入不能为bam ，最好先对bam进行过滤再split，节省时间）文件，以一号染色体为例：
sam_split.py filtered.1.sam -p split ####split1.sam
predict_graphs.py filtered.1.sam.gz.sam -m chr1.gtf -v -d predicted （多基因调用这个程序）
predict_splicegraph.py ENSG00000160072 -d filtered.1.sam.gz.sam -m chr1.gtf -o ENSG00000227232.gff -v


* 对于blat 和gmap比对输出的psl格式文件;
ests_to_splicegraph.py gmap_alignments.psl -d est_splicegraphs -i 10
-i 制定最小intron长度，默认为4nt，有时2个exon靠的较近，splice site可能判断错误

* gene_model_to_splicegraph.py -m chr1.gtf -a -S -d predicted/
这里输入的gtf文件应该用转录组重构的结果，最好？这样的话，需要将stringtie的输出中gene_id 属性换做ref_gene_id ，python /data/BIN/zhush/bin/deepTools2.0/SpliceGrapher-0.2.4/renewGeneID4stringtie.py stringtie.gtf
可以生成全部所有基因的graph