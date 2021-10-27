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
![image](https://user-images.githubusercontent.com/93254228/139106716-fc698ed1-bc4b-450d-918e-ac477bd5076b.png)
После:
![image](https://user-images.githubusercontent.com/93254228/139107030-1b697598-cba6-4479-af16-f18fe568631b.png)
До:
![fastqc_per_base_sequence_quality_plot](https://user-images.githubusercontent.com/93254228/139107255-cd4f91f1-d8c4-4c1f-afa7-c50a0cc11828.png)
После:
![fastqc_per_base_sequence_quality_plot (2)](https://user-images.githubusercontent.com/93254228/139107404-0a72b82b-edec-41d4-b6f6-91071e113db5.png)
До:
![fastqc_adapter_content_plot](https://user-images.githubusercontent.com/93254228/139107722-e90c7234-980a-46cd-a0e3-b4659ab9cdf7.png)
После:
![fastqc_adapter_content_plot (2)](https://user-images.githubusercontent.com/93254228/139107851-7398e86e-3cf6-48ef-898a-2af14592b031.png)
9. Используя программу platanus assemble, собираем контиги из подрезанных чтений:
```
time platanus assemble -o Poil -f trimmed_fastq/pe_R1.fastq.trimmed trimmed_fastq/pe_R2.fastq.trimmed 2> assemble.log
```
10. Далее перейдём в Google Colab. Там проанализируем полученные контиги:
![image](https://user-images.githubusercontent.com/93254228/139109551-93966034-fccd-487e-9f1c-c7114589c01f.png)
