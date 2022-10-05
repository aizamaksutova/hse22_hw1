# hse22_hw1
bioinformatics homework №1

1. Копируем файлы, чтобы можно было с ними работать, предварительно создав папку homework и перейдя в нее
```
$ mkdir homework1
$ cd homework1
$ ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
$ ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
$ ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
$ ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
```
2. Проверяем есть ли свободные процессоры
```
$ htop
$ seqtk sample -s0226 oil_R1.fastq 5000000 > sub1.fastq
$ seqtk sample -s0226 oil_R2.fastq 5000000 > sub2.fastq
$ seqtk sample -s0226 oilMP_S4_L001_R1_001.fastq 1500000 > subMP1.fastq
$ seqtk sample -s0226 oilMP_S4_L001_R2_001.fastq 1500000 > subMP2.fastq
```
3. С помощью программы fastQC и multiQC оценить качество исходных чтений и получить по ним общую статистику. Сперва создаем директорию fastqc_results(у меня resulrs, я немного опечаталась) и выполняем команды для оценки и получения статистики

```
$ mkdir quality_resulrs
$ fastqc sub1.fastq sub2.fastq subMP1.fastq subMP2.fastq -o quality_resulrs
$ multiqc quality_resulrs -o multiqc_res
```

результат можно получить вот в этом файле https://github.com/aizamaksutova/hse22_hw1/blob/main/multiqc_report.html
либо ниже приведу картинки;)
![multiqc_report](/images/stats.png)
![multiqc_report1](/images/stats1.png)
