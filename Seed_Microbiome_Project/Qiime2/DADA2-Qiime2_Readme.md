# DADA2 Pipeline in Qiime2 FP 

Check Readme.md file for how to install Qiime2 on WSL 
If you need any help during the pipeline use the following command: 

    qiime --help

For visualizing qza, qzv files: https://view.qiime2.org/

## Before you start: Activate qiime2 plugin in your terminal

    conda activate qiime2-amplicon-2024.2
    #conda activate qiime2-shotgun-2024.2

## 1. Importing your data
**Source**: https://docs.qiime2.org/2024.5/tutorials/importing/

**Format description** <br>
In Casava 1.8 demultiplexed (paired-end) format, there are two fastq.gz files for each sample in the study, each containing the forward or reverse reads for that sample. The file name includes the sample identifier. The forward and reverse read file names for a single sample might look like L2S357_15_L001_R1_001.fastq.gz and L2S357_15_L001_R2_001.fastq.gz, respectively. The underscore-separated fields in this file name are:
the sample identifier, the barcode sequence or a barcode identifier, the lane number, the direction of the read (i.e. R1 or R2), and the set number. <br>

One of my example files: ITS-WS1_S45_L001_R1_001 + ITS-WS1_S45_L001_R2_001  <br>
**Note**: My samples are already demultiplexed so I will indicate this in the output file <br>

    qiime tools import  --type 'SampleData[PairedEndSequencesWithQuality]' --input-path /home/frapi/SMP_2024/importing_SMP_data   --input-format CasavaOneEightSingleLanePerSampleDirFmt --output-path /home/frapi/SMP_2024/T3_4JUNE/SMP_demux-paired-end_FP.qza

Check that your imported sequences are in the correct format - confirming that import worked as expected: 

    qiime tools peek SMP_demux-paired-end_FP.qza

Generate summary of the demultiplexnig results. Here we will be able to see how many sequences were obtained per sample, and also to get a summary of the distribuion of sequence qualities at each position in your sequence data. 

    qiime demux summarize \
      --i-data SMP_demux-paired-end_FP.qza \
      --o-visualization SMP_demux-paired-end_FP.qzv

This command will produce the .qzv file that you can view using either this website: https://view.qiime2.org/ or this command: 

      qiime tools view SMP_demux-paired-end_FP.qzv

## 2. Denoising using DADA2 plugin 

**Source**: https://docs.qiime2.org/2024.5/plugins/available/dada2/denoise-paired/ <br>

DADA2 is a pipeline for detecting and correcting Illumina amplicon sequence data. <br>

    qiime dada2 denoise-paired \
      --i-demultiplexed-seqs SMP_demux-paired-end_FP.qza \
      --p-trim-left-f 25 \
      --p-trim-left-r 25 \
      --p-trunc-len-f 300 \
      --p-trunc-len-r 300 \
      --o-representative-sequences SMP_DADA2_Trim25_Trunc250_FP.qza \
      --o-table SMP_DADA2_table_Trim25_Trunc300_FP.qza \
      --o-denoising-stats SMP_DADA2_stats_Trim25_Trunc300_FP.qza

Need to find strategy to determine what the best cut-off point is for these ones - focus on this tomorrow. Check notebook