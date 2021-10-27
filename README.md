# hse21_hw1
1. Создадим папку для домашних работ hw и в ней сделаем папку 1 для первой домашки. А также получим нужные файлы (последняя строка)
```
mkdir hw
cd hw
mkdir 1
cd 1
ls -1 /usr/share/data-minor-bioinf/assembly/* | xargs -tI{} ln -s {}
```
2. Выберем случайные чтения, используя наш random seed (август = 8 месяц, 31 число)
```
seqtk sample -s831 oil_R1.fastq 5000000 > pe_R1.fastq
seqtk sample -s831 oil_R2.fastq 5000000 > pe_R2.fastq
seqtk sample -s831 oilMP_S4_L001_R1_001.fastq 1500000 > mp_R1.fastq
seqtk sample -s831 oilMP_S4_L001_R2_001.fastq 1500000 > mp_R2.fastq
```
3. Далее удалим ненужные файлы
```
rm -r oil_R1.fastq
rm -r oil_R2.fastq
rm -r oilMP_S4_L001_R1_001.fastq
rm -r oilMP_S4_L001_R2_001.fastq
```
4. Используя программы fastQC и multiQC, оценим качества исходных чтений и получим общую статистику по ним (для скачивания файлов с сервера на компьютер использовалась программа WinSCP)
```
mkdir fastqc
ls *.fastq | xargs -P 4 -tI{} fastqc -o fastqc {}

mkdir multiqc
multiqc -o multiqc fastqc
```
5. Далее используем программы platanus_trim и platanus_internal_trim, подрезаем чтения по качеству, а также удаляем праймеры
```
platanus_trim pe_R1.fastq pe_R2.fastq 
platanus_internal_trim mp_R1.fastq mp_R2.fastq 
```
6. Теперь удалим ненужные файлы
```
ls -1 *.fastq | xargs -tI{} rm -r {}
```
7. Далее оценим качества подрезанных чтений и получим по ним общую статистику (используем программы fastQC и multiQC)
```
mkdir trimmed_fastq
mv -v *trimmed trimmed_fastq/

mkdir trimmed_fastqc
ls trimmed_fastq/* | xargs -P 4 -tI{} fastqc -o trimmed_fastqc {}

mkdir trimmed_multiqc
multiqc -o trimmed_multiqc trimmed_fastqc
```
До:
![image](file:///C:/Users/User/Desktop/html/multiqc_report.html#general_stats)
