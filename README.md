# Calculating-and-plotting-Fst
Calculating and plotting Fst from a VCF file

# Get sample names from a vcf 
```bash
vcf-query -l input.vcf
```
select sample sets from above (sample names/for different populations etc.) and save them in seperate files(can use nano/Vi)
eg: as males and females

***********old********************************
# Recode- all chromosomes
```
for i in Chr1L Chr1S Chr2L Chr2S Chr3L Chr3S Chr4L Chr4S Chr5L Chr5S Chr6L Chr6S Chr7L Chr7S Chr8L Chr8S Chr9_10L Chr9_10S; do vcftools --vcf mpileup_raw_wildBorealis_AustinGenome.vcf --chr $i --recode --recode-INFO-all --out $i; done
```

# Calculating Fst- all chromosomes
```
for i in Chr1L Chr1S Chr2L Chr2S Chr3L Chr3S Chr4L Chr4S Chr5L Chr5S Chr6L Chr6S Chr7L Chr7S Chr8L Chr8S Chr9_10L Chr9_10S; do vcftools --vcf splitted/$i\.recode.vcf --weir-fst-pop females --weir-fst-pop males --fst-window-size 500000 --fst-window-step 500000 --out out/Fst_Cam_F_Vs_M_$i; done

```
************************************************

# combining above both

,,,bash
for i in Chr1 Chr2 Chr3 Chr Chr5 Chr6 Chr7 Chr8 Chr9 Chr10; do vcftools --vcf ../vcf_input/Trop_merged_sorted.bam.vcf --chr $i --weir-fst-pop ../populations/females --weir-fst-pop ../populations/males --fst-window-size 500000 --fst-window-step 500000 --out ../out/Fst_all_with_sierra_F_Vs_M_$i; done
```
# Download
```
tharindu@info.mcmaster.ca:/net/infofile2/2/scratch/tharindu/splitchromosomes/splitted/step50000/west/* ./west
```
Download the files into seperate folders according to populations so it will be easier for plotting.

# Plot all the chromosomes of all the populations at once

```R
all_populations<-c("Cam")

#**********************************
for (current_pop in all_populations) {
  
  
  #population
  pop<-current_pop
  
  #**********************************
  
  setwd(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/allofraseri/Fst/",pop,sep = ''))
  
  
  library(ggplot2)
  plot_list<-list()
  x<-0
  
  #remove scientific notation
  options(scipen=999)
  
  ch_numbers<-c("chr1L","chr1S","chr2L","chr2S","chr3L","chr3S","chr4L","chr4S","chr5L","chr5S","chr6L","chr6S","chr7L","chr7S","chr8L","chr8S","chr9_10L","chr9_10S")
  for (i in ch_numbers) {
    chrom_data<-read.table(paste("Fst_",pop,"_F_VS_M_",i,".windowed.weir.fst",sep=''),header = T)
    attach(chrom_data)
    
    x<-x+1
    
    
    
    a<-(ggplot(chrom_data,aes(x=BIN_START,y=MEAN_FST,col=MEAN_FST))+
          geom_point()+
          labs(title = paste("Mean_Fst_",current_pop,i))
        +ylim(-1, 1))
    plot_list[[x]]=a
  }
  
  
  
  library(gridExtra)
  Final_plot_grid<-grid.arrange(
    grobs = plot_list,
    ncol=2
    
    #widths = c(2, 2),
    #heights=c(2,1)
  )
  
  #Sys.sleep(10)
  
  print("done")
  
  
  
  #ggsave(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/Pi/step50000/Borealis/Different_populations/",pop,"/",pop,"_PI_mean_substracted.pdf",sep = ''),plot = Final_plot_grid,width = 15,height = 30)
  #ggsave(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/Pi/step50000/Borealis/Different_populations/all_populations_PI-mean/",pop,"_PI_mean_substracted.pdf",sep = ''),plot = Final_plot_grid,width = 15,height = 30)
  ggsave(paste("~/Desktop/OneDrive - McMaster University/Tharindu on Mac/lab/allofraseri/Fst/",pop,"_Mean_Fst_Male_VS_Female.pdf",sep = ''),plot = Final_plot_grid,width = 15,height = 30)
  #*************************************
}
#************************************
```
