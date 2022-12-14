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
$ mkdir multiqc
$ multiqc quality_resulrs -o multiqc_res
```

результат можно получить вот в этом файле https://github.com/aizamaksutova/hse22_hw1/blob/main/multiqc1_report.html
либо ниже приведу картинки;)
![multiqc_report](/images/stats.png)
![multiqc_report1](/images/stats1.png)

4. Подрезаем чтения по качеству и удаляем адаптеры
```
$ platanus_trim sub1.fastq sub2.fastq
```
```
Number of trimmed read with adapter: 
NUM_OF_TRIMMED_READ(FORWARD) = 209273
NUM_OF_TRIMMED_BASE(FORWARD) = 209689
NUM_OF_TRIMMED_READ(REVERSE) = 209325
NUM_OF_TRIMMED_BASE(REVERSE) = 357828
NUM_OF_TRIMMED_PAIR(OR) = 209344
NUM_OF_TRIMMED_PAIR(AND) = 209254


Number of trimmed read because of low quality or too short (< 11bp): 
NUM_OF_TRIMMED_READ(FORWARD) = 908801
NUM_OF_TRIMMED_BASE(FORWARD) = 18125777
NUM_OF_TRIMMED_READ(REVERSE) = 1179538
NUM_OF_TRIMMED_BASE(REVERSE) = 35920617
NUM_OF_TRIMMED_PAIR(OR) = 1631261
NUM_OF_TRIMMED_PAIR(AND) = 457078
```
```
$ platanus_internal_trim subMP1.fastq subMP2.fastq
```
```
Number of trimmed read with internal adapter: 
NUM_OF_TRIMMED_READ(FORWARD) = 971862
NUM_OF_TRIMMED_BASE(FORWARD) = 169612617
NUM_OF_TRIMMED_READ(REVERSE) = 956043
NUM_OF_TRIMMED_BASE(REVERSE) = 171384290
NUM_OF_TRIMMED_PAIR(OR) = 1187937
NUM_OF_TRIMMED_PAIR(AND) = 739968


Number of trimmed read with adapter: 
NUM_OF_TRIMMED_READ(FORWARD) = 11098
NUM_OF_TRIMMED_BASE(FORWARD) = 367383
NUM_OF_TRIMMED_READ(REVERSE) = 11123
NUM_OF_TRIMMED_BASE(REVERSE) = 389054
NUM_OF_TRIMMED_PAIR(OR) = 11129
NUM_OF_TRIMMED_PAIR(AND) = 11092


Number of trimmed read because of low quality or too short (< 11bp): 
NUM_OF_TRIMMED_READ(FORWARD) = 360733
NUM_OF_TRIMMED_BASE(FORWARD) = 11656748
NUM_OF_TRIMMED_READ(REVERSE) = 468448
NUM_OF_TRIMMED_BASE(REVERSE) = 23919065
NUM_OF_TRIMMED_PAIR(OR) = 724563
NUM_OF_TRIMMED_PAIR(AND) = 104618

```
после подрезания чтений удаляем исходные .fastq файлы, полученные с помощью программы seqtk
```
$ rm sub1.fastq sub2.fastq
$ rm subMP1.fastq subMP2.fastq
```
С помощью программы fastQC и multiQC оценим качество подрезанных чтений и получим по ним общую статистику
```
$ mkdir fastqc_trimmed_result                        
$ fastqc -o fastqc_trimmed_result sub1.fastq.trimmed sub2.fastq.trimmed subMP1.fastq.int_trimmed subMP2.fastq.int_trimmed
$ mkdir multiqc_trimmed
$ multiqc -o multiqc_trimmed fastqc_trimmed_result

```
Результаты можно получить по ссылке https://github.com/aizamaksutova/hse22_hw1/blob/main/multiqc_report_trimmed.html
Но приведу картинки здесь
![m1](/images/stat1.png)
![m2](/images/stat2.png)
![m3](/images/stat3.png)

С помощью программы “platanus assemble” собираем контиги из подрезанных чтений(platanus assemble), потом собираем контиги из контигов и подрезанных чтений(platanus scaffold), а также в конце уменьшаем кол-во гэпов с помощью подрезанных чтений(platanus gap_close)
```
$ platanus assemble –o Oil –f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
$ platanus scaffold –o Oil –c Oil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 subMP1.fastq.int_trimmed subMP2.fastq.int_trimmed 2> scaffold.log
$ platanus gap_close –o Oil –c Oil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 subMP1.fastq.int_trimmed subMP2.fastq.int_trimmed 2> gapclose.log

```
Удалим все ненужные fastq файлы
```
$ rm -rf sub1.fastq.trimmed sub2.fastq.trimmed subMP1.fastq.int_trimmed subMP2.fastq.int_trimmed
```


Бонусное задание
```
seqtk sample -s226 oil_R1.fastq 500000 > bonus_sub_oilR1.fastq
seqtk sample -s226 oil_R2.fastq 500000 > bonus_sub_oilR2.fastq
seqtk sample -s226 oilMP_S4_L001_R1_001.fastq 150000 > bonus_sub_oilMP_R1.fastq
seqtk sample -s226 oilMP_S4_L001_R2_001.fastq 150000 > bonus_sub_oilMP_R2.fastq

platanus_trim bonus_sub_oilR*
platanus_internal_trim bonus_sub_oilMP*

platanus assemble -o Poil -f bonus_sub_oilR1.fastq.trimmed bonus_sub_oilR2.fastq.trimmed 2> bonus_assemble.log

platanus scaffold -o Poil -c Poil_contig.fa -IP1 bonus_sub_oilR1.fastq.trimmed bonus_sub_oilR2.fastq.trimmed -OP2 bonus_sub_oilMP_R1.fastq.int_trimmed bonus_sub_oilMP_R2.fastq.int_trimmed 2> bonus_scaffold.log

platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 bonus_sub_oilR1.fastq.trimmed bonus_sub_oilR2.fastq.trimmed -OP2 bonus_sub_oilMP_R1.fastq.int_trimmed bonus_sub_oilMP_R2.fastq.int_trimmed 2> bonus_gapclose.log

rm bonus
```
