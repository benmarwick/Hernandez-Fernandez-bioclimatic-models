---
title: "Partial reproduction of the LDA models and predictions from Hernández Fernández 2001 & 2003"
author: "Ben Marwick and Gillian Wong"
date: "29 June, 2019"
output: 
 bookdown::html_document2:
   keep_md: true
---





### Hernández Fernández (2001) and the whole mammal bioclimatic model {-#whole}

Hernández Fernández (2001, 2003) proposed a bioclimatic model for palaeoclimatic reconstruction based on the assumption of a correlation between climate and mammal community composition. 

In his 2001 paper we see table 2 which has data for whole mammal faunal bioclimatic spectra of the localities. Each column represents a bioclimatic component, and BCi values. From Appendix 1 of the 2001 paper we can get the actual climatic zones for the localities in table 2. Using these two tables we can make a labelled training set to train a machine learning model that can predict the climate zone of new faunal assemblages. 


```r
library(docxtractr)
library(tidyverse)
# get the BCi and Cl. values from https://onlinelibrary.wiley.com/doi/full/10.1046/j.1466-822x.2001.00218.x
# Table 2 Whole mammal faunal bioclimatic spectra of the localities. Each column 
# represents a bioclimatic component (see Table 1), and BCi values 
# Appendix 1 Characteristics of studied localities (Cl., climate zone)

fh_2001_tbl_2 <- read_csv(here::here("data/Fernández-2001-table-2.csv"))
fh_2001_tbl_appx_1 <- read_csv(here::here("data/Fernández-2001-table-appendix1.csv"))

fh_2003_tbl_appx_1.1 <- # Whole mammal faunal bioclimatic spectra, same as fh_2001_tbl_2
  docx_extract_tbl(read_docx(here::here("data/geb_057_sm_appendix1.1.doc")))

fh_2001_BCi_values_and_Cl <- 
  left_join(fh_2001_tbl_2, 
            fh_2001_tbl_appx_1) %>% 
  dplyr::select(-c(
                   Latitude,  
                   Longitude, 
                   Altitude,
                   References,
                   Country,
                   N))

library(knitr)
kable(fh_2001_BCi_values_and_Cl, caption = "Whole mammal faunal bioclimatic spectra from 50 locations")
```



Table: (\#tab:fh-2001-data)Whole mammal faunal bioclimatic spectra from 50 locations

Locality                      I       II   II/III      III       IV        V       VI      VII     VIII       IX  Cl.    
----------------------  -------  -------  -------  -------  -------  -------  -------  -------  -------  -------  -------
Barrow                    0.000    1.209    1.209    1.890    3.794    2.162   10.540    7.366   31.969   39.860  IX     
Port Harrison             0.000    0.794    0.794    1.814    4.671    3.243   15.385   13.005   22.528   37.766  IX     
Malye‐Karmaerly           0.000    1.587    1.587    3.628    3.628    3.628    3.628    3.628   25.057   53.628  IX     
Mys Chelyuskin            0.000    1.235    1.235    1.235    1.235    1.235    4.938    1.235   32.716   54.938  IX     
Mys Schmidta              0.000    0.694    0.694    0.694    1.944    0.694    7.674    3.507   42.049   42.049  IX     
Fairbanks                 0.000    0.705    2.584    2.055    4.416    3.463   22.101    9.601   45.712    9.363  VIII   
Fort Smith                0.605    1.209    4.293    3.840    5.523    4.849   22.808   12.887   37.944    6.041  VIII   
Kajaani                   0.726    1.043    0.635    1.043    8.166    2.737   25.546    9.594   41.737    8.773  VIII   
Serov                     0.518    0.745    0.454    0.745    7.873    2.465   26.241   10.764   43.928    6.267  VIII   
Erbogachen                0.876    1.259    0.766    0.766    4.822    2.121   12.293    7.121   55.397   14.579  VIII   
Rapid City                1.139    3.505    8.216    8.641    9.934    6.575   19.718   31.769    8.907    1.596  VII    
Puerto Santa Cruz         2.105    4.142   14.050    7.661    9.883   11.272   15.849   34.420    0.617    0.000  VII    
Fort Schevchenko          1.675    2.215    1.506    5.052   16.491    2.342    8.619   58.973    2.160    0.966  VII    
Urumchi                   0.926    1.296    1.296    2.963    9.574    1.963    8.741   65.963    5.407    1.870  VII    
Paotou                    0.236    0.777    1.202    2.925    7.322    2.038    8.386   72.570    3.578    0.966  VII    
Prince Rupert             0.309    1.080    4.837    3.911    6.411    4.698   28.726   15.763   29.559    4.707  VI     
Cleveland                 1.858    4.685    5.093    4.665    5.982   11.677   33.470   14.304   16.121    2.146  VI     
Evangelistas              1.458    4.887   11.554    8.284   11.169   12.900   30.876   17.964    0.908    0.000  VI     
Vlissingen                0.968    1.587    1.239    1.587   20.066    3.643   41.814   10.368   15.710    3.018  VI     
Tsingtao                  5.474    6.821    3.862    1.539    8.314   16.193   37.658   14.952    4.418    0.770  VI     
New Orleans               5.451    9.355    6.650    5.194    5.021   37.883   17.542    8.641    3.938    0.325  V      
Puerto Montt              1.723    5.245    7.291    4.942   17.367   42.518   13.308    6.533    1.073    0.000  V      
Corrientes               17.391   32.869   18.115    1.612    1.694   25.219    1.284    1.612    0.205    0.000  V      
Foochow                  16.255   22.343    5.595    1.038    3.873   33.804   12.685    2.340    1.916    0.152  V      
Kagoshima                 5.659    6.770    2.663    1.687    4.627   36.085   34.085    3.020    4.270    1.130  V      
Fresno                    2.016    5.462   11.794   12.429   29.435    4.620   13.306   13.632    6.423    0.882  IV     
Santiago de Chile         2.298    4.690   12.432   12.217   37.701   13.131   10.251    6.518    0.762    0.000  IV     
Sanlucar de Barrameda     2.310    4.430    3.372    4.113   39.896    6.303   24.266    9.690    4.610    1.009  IV     
Aleppo                    2.026    5.589    6.517    8.739   41.271    5.354   15.299   11.684    2.763    0.757  IV     
Esfahan                   0.577    3.276    4.601   10.608   44.282    3.979   11.668   16.912    3.065    1.032  IV     
Phoenix                   2.772    9.240   21.504   27.371   10.326    4.266    7.427   13.490    3.452    0.152  III    
Arica                     5.177    5.177   15.025   40.783   18.056    8.207    2.146    4.419    1.010    0.000  III    
Assuan                    2.676   13.027   16.360   37.127   17.451    5.831    3.146    2.584    1.400    0.397  III    
Mascate                   2.450   11.919   13.368   34.279   22.685    5.293    2.830    4.486    1.587    1.104  III    
Lüderitz Bay              5.537   13.146   21.003   37.146   14.527    8.027    0.408    0.204    0.000    0.000  III    
Las Piedras              30.769   32.491   28.788    1.170    0.768    4.541    0.521    0.775    0.175    0.000  II/III 
Catamarca                 7.615   18.742   44.514    5.594    3.773   11.581    2.088    5.655    0.437    0.000  II/III 
Zinder                    6.485   27.304   37.145   12.637    7.510    8.510    0.272    0.136    0.000    0.000  II/III 
Voi                       8.378   36.424   29.066   10.615    6.434    8.832    0.167    0.084    0.000    0.000  II/III 
Jaipur                   14.233   29.296   33.831   10.802    5.130    3.904    1.723    1.082    0.000    0.000  II/III 
Roque Saenz Peña         15.817   33.237   24.583    2.660    1.891   18.686    1.250    1.635    0.240    0.000  II     
Ziguinchor               16.192   39.272   21.434    8.928    5.980    7.839    0.236    0.118    0.000    0.000  II     
Moundou                  11.124   39.671   24.726    8.432    6.379    9.313    0.236    0.118    0.000    0.000  II     
Mtwara                   11.150   46.505   19.326    7.148    5.198   10.394    0.187    0.093    0.000    0.000  II     
Patna                    17.398   38.922   28.207    6.551    3.660    3.303    1.469    0.490    0.000    0.000  II     
Tumaco                   44.932   28.115   19.003    1.137    0.746    4.926    0.363    0.609    0.170    0.000  I      
Paramaribo               46.245   29.179   18.401    0.686    0.361    4.488    0.227    0.347    0.067    0.000  I      
Kribi                    69.971   17.420    5.558    2.360    1.799    2.819    0.073    0.000    0.000    0.000  I      
Yangambi                 65.496   19.607    5.950    2.774    2.274    3.820    0.079    0.000    0.000    0.000  I      
Medan                    69.883   20.111    5.002    0.612    0.522    2.588    0.990    0.196    0.098    0.000  I      

Using these data, we can roughly follow Hernández Fernández's method to compute a Linear Discriminant Analysis to generate a model to predict the climate zone of a new faunal assemblage:


```r
library(caret)
set.seed(998)

fh_2001_BCi_values_and_Cl <- select(fh_2001_BCi_values_and_Cl, -Locality)

inTraining1 <- createDataPartition(fh_2001_BCi_values_and_Cl$Cl., 
                                  p = .75, 
                                  list = FALSE)

training1 <- fh_2001_BCi_values_and_Cl[ inTraining1,]
testing1  <- fh_2001_BCi_values_and_Cl[-inTraining1,]

# estimate pre processing parameters
# center and scale
preProcValues1 <- preProcess(training1, method = c("center", "scale"))

trainTransformed1 <-  
 predict(preProcValues1, training1)

testTransformed1 <- 
 predict(preProcValues1, testing1)

# now run DFA using Linear Discriminant analysis (LDA) on the normalized data 

# Basic Parameter Tuning
fitControl <- trainControl(## 10-fold CV
  method = "repeatedcv",
  number = 10,
  ## repeated ten times
  repeats = 10)

# fit the model to the training data
ldaFit1 <- train(Cl. ~ ., 
                 trControl = fitControl,
                 data = trainTransformed1, 
                 method = "lda")
```

We can inspect the performance of this model on the training set, and we can see one location mis-classified 


```r
# model against the training set 
lda_train <- predict(MASS::lda(Cl. ~ ., data = trainTransformed1))
trainTransformed1$lda <- lda_train$class
kable(table(trainTransformed1$lda, trainTransformed1$Cl.),
      caption = "Model performance with training data")
```



Table: (\#tab:fh-model1-training)Model performance with training data

           I   II   II/III   III   IV   IX    V   VI   VII   VIII
-------  ---  ---  -------  ----  ---  ---  ---  ---  ----  -----
I          4    0        0     0    0    0    0    0     0      0
II         0    4        1     0    0    0    0    0     0      0
II/III     0    0        3     0    0    0    0    0     0      0
III        0    0        0     4    0    0    0    0     0      0
IV         0    0        0     0    4    0    0    0     0      0
IX         0    0        0     0    0    4    0    0     0      0
V          0    0        0     0    0    0    4    0     0      0
VI         0    0        0     0    0    0    0    4     0      0
VII        0    0        0     0    0    0    0    0     4      0
VIII       0    0        0     0    0    0    0    0     0      4

And we can inspect the model on the testing data set, and we see less accuracy:


```r
# use this model to predict the classifications of the test data
test_predictions1 <- 
  predict(ldaFit1, 
          newdata = testTransformed1)
# model against the testing set 
kable(table(test_predictions1, testTransformed1$Cl.),
      caption = "Model performance with hold-out test data")
```



Table: (\#tab:fh-model1-testing)Model performance with hold-out test data

           I   II   II/III   III   IV   IX    V   VI   VII   VIII
-------  ---  ---  -------  ----  ---  ---  ---  ---  ----  -----
I          1    0        0     0    0    0    0    0     0      0
II         0    0        0     0    0    0    0    0     0      0
II/III     0    0        1     0    0    0    0    0     0      0
III        0    0        0     1    0    0    0    0     0      0
IV         0    0        0     0    1    0    0    0     0      0
IX         0    0        0     0    0    1    0    0     0      0
V          0    1        0     0    0    0    1    0     0      0
VI         0    0        0     0    0    0    0    0     0      0
VII        0    0        0     0    0    0    0    0     1      0
VIII       0    0        0     0    0    0    0    1     0      1

```r
pred.accuracy = round(mean(test_predictions1 == testTransformed1$Cl.) * 100, 2)
```

And we find a prediction accuracy of 80 % in this case, not bad. 

In Appendix 3, Hernández Fernández (2001) shows the results of his LDA model applied to the locations in his training data set. He appears to have tested his model on the same data set that he used to train it, which is not ideal. Perhaps not suprisingly, there is a very good fit, and he claims a 98% correct classification rate. Only one location was incorrect, the same as we observed with our training data. From these results we can conclude that we have successfully reproduced some key results of Hernández Fernández (2001). 

### Hernández Fernández (2003) and whole fauna biospectra at 13 new locations {-#new}

In his 2003 paper, Hernández Fernández attempts to infer past climatic conditions using mammal fossil associations as source data. In this 2003 paper we also have 13 new recent faunas presented for testing. So let's attempt to reproduce Hernández Fernández's results with these 13 new fauna samples. We will train a LDA model on all 50 locations from the 2001 paper. Then we will use this model to classify the climate zones of these 13 new locations in the 2003 paper. Then we'll check to see how accurate the model was with these 13 new locations. This is a direct reproduction of the key results in the 2003 paper. 

Here are the 13 new locations from appendix 3.1 in the 2003 paper that we will use as the test dataset, just as Hernández Fernández does:


```r
hf_2003_appdx_3.1 <- 
  read_docx(here::here("data/geb_057_sm_appendix 3.1.doc")) %>% 
  docx_extract_tbl() 

# get the table from the appendix word doc
hf_2003_appdx_3.1_tbl <-
  hf_2003_appdx_3.1 %>%
  mutate_at(vars(2:11), ~ parse_number(.)) %>%
  dplyr::select(-Whole.mammal) %>%
  set_names(c('I', 'II', 'II/III', 'III', 'IV', 'V',  'VI',  'VII', 'VIII', 'IX'))

kable(hf_2003_appdx_3.1, caption = "Whole mammal biospectra from 13 new locations")
```



Table: (\#tab:fh-model2-import-data)Whole mammal biospectra from 13 new locations

Whole.mammal      I        II       II.III   III      IV       V        VI       VII      VIII     IX     
----------------  -------  -------  -------  -------  -------  -------  -------  -------  -------  -------
Ust Kamchatsk     0.571    1.016    0.444    1.016    4.721    1.587    8.387    4.387    52.387   25.483 
Saskatoon         1.298    1.963    5.113    4.779    6.604    6.107    24.311   18.755   27.496   3.574  
Riga              1.124    1.356    1.058    1.058    12.970   3.112    39.883   9.897    24.878   4.661  
Budapest          0.818    1.203    1.289    1.794    20.560   3.237    42.656   16.556   10.517   1.370  
Dairen            5.451    5.809    2.929    1.639    7.883    14.442   32.291   13.336   13.144   3.077  
Portland          1.492    2.966    6.119    6.940    16.004   5.704    34.021   12.428   12.022   2.306  
Charleston        4.803    7.690    5.254    4.539    5.138    39.152   19.113   10.005   3.975    0.332  
Niza              1.600    2.298    1.873    2.386    30.236   4.364    37.236   10.221   8.705    1.083  
Kweilin           20.065   23.732   6.279    1.127    2.598    31.465   11.132   2.194    1.408    0.000  
Smara             4.359    6.996    15.971   35.018   23.480   5.018    2.198    4.762    1.099    1.099  
Puerto Ayacucho   31.994   33.612   26.749   1.112    0.566    4.886    0.426    0.573    0.082    0.000  
Tabou             63.442   20.721   5.811    3.453    2.671    3.805    0.097    0.000    0.000    0.000  
Kuala Lumpur      71.967   18.593   4.840    0.499    0.536    2.386    0.955    0.156    0.068    0.000  

Here is the LDA model that we generate from all 50 locations in the 2001 paper.  Hernández Fernández used SPSS, so we don't know the exact configuration of his model. But we can see elshwere online that R and SPSS can give good agreement with LDA.


```r
library(caret)
set.seed(998)

training2 <- fh_2001_BCi_values_and_Cl

# estimate pre processing parameters
# center and scale
preProcValues2 <- preProcess(training2, method = c("center", "scale"))

trainTransformed2 <-  
 predict(preProcValues2, training2)

# now run DFA using Linear Discriminant analysis (LDA) on the normalized data 

# Basic Parameter Tuning
fitControl <- trainControl(## 10-fold CV
  method = "repeatedcv",
  number = 10,
  ## repeated ten times
  repeats = 10)

# fit the model to the training data
ldaFit2 <- train(Cl. ~ ., 
                 trControl = fitControl,
                 data = trainTransformed2, 
                 method = "lda")
```

Table 2 in the 2003 paper shows the actual Climate zones for the 13 new locations, so let's import that here so we can compare with our model's predictions:


```r
fh_2003_tbl_2 <- read_csv(here::here("data/Fernández-2003-table-2.csv"))
fh_2003_tbl_2_no_na <- filter(fh_2003_tbl_2, !is.na(`Climate zone`))

kable(fh_2003_tbl_2_no_na, caption = "Actual climate zones of the 13 new locations")
```



Table: (\#tab:fh-model2-import-more)Actual climate zones of the 13 new locations

  N  Climate zone   Locality          Country       Latitude   Longitude   Altitude   References                                               
---  -------------  ----------------  ------------  ---------  ----------  ---------  ---------------------------------------------------------
 51  VIII/IX        Ust Kamchatsk     Russia        56°14′‐N   162°28′‐E   6 m        Corbet (1978)                                            
 52  VI/VIII        Saskatoon         Canada        52°08′‐N   106°38′‐W   157 m      Hall (1981)                                              
 53  VI/VIII        Riga              Latvia        56°58′‐N   24°04′‐E    3 m        Corbet (1978)                                            
 54  VI/VII         Budapest          Hungary       47°31′‐N   19°02′‐E    120 m      Corbet (1978)                                            
 55  VI             Dairen            China         38°54′‐N   121°38′‐E   96 m       Corbet (1978)                                            
 56  IV/VI          Portland          USA           45°32′‐N   122°40′‐W   9 m        Hall (1981)                                              
 57  V              Charleston        USA           32°54′‐N   80°02′‐W    100 m      Hall (1981)                                              
 58  IV/VI          Niza              France        43°40′‐N   7°12′‐E     5 m        Corbet (1978)                                            
 59  II/V           Kweilin           China         25°15′‐N   110°10′‐E   167 m      Corbet (1978);                                           
 60  III            Smara             Sahara W      26°44′‐N   11°26′‐W    140 m      Corbet (1978)                                            
 61  II             Puerto Ayacucho   Venezuela     5°41′‐N    67°38′‐W    99 m       Eisenberg (1989)                                         
 62  I/II           Tabou             Ivory Coast   4°55′‐N    7°22′‐E     4 m        Kingdon (1971–82); Dorst & Dandelot (1973); Nowak (1991) 
 63  I              Kuala Lumpur      Malaysia      3°07′‐N    101°42′‐E   34 m       Corbet & Hill (1992)                                     

Now we can inspect the model on the testing data set of the 13 new locations from the 2003 paper. Below we can see a pretty accurate classification from our model's predictions and the actual climate zone reported in Hernández Fernández (2003) table 2:


```r
# use this model to predict the classifications of the test data on the 13 new locations
testing2  <- hf_2003_appdx_3.1_tbl

testTransformed2 <- 
 predict(preProcValues2, testing2)

test_predictions_ldaFit2 <- 
  predict(ldaFit2, 
          newdata = testTransformed2)

# model against the testing set 
kable(tibble(Location = hf_2003_appdx_3.1$Whole.mammal, 
       `Predicted by our model`= test_predictions_ldaFit2,
       `Actual zone from table 2` = fh_2003_tbl_2_no_na$`Climate zone`),
      caption = "Model predictions versus actual climate zones of the 13 new locations")
```



Table: (\#tab:fh-model2-compare)Model predictions versus actual climate zones of the 13 new locations

Location          Predicted by our model   Actual zone from table 2 
----------------  -----------------------  -------------------------
Ust Kamchatsk     VIII                     VIII/IX                  
Saskatoon         VI                       VI/VIII                  
Riga              VI                       VI/VIII                  
Budapest          VI                       VI/VII                   
Dairen            VI                       VI                       
Portland          VI                       IV/VI                    
Charleston        V                        V                        
Niza              IV                       IV/VI                    
Kweilin           V                        II/V                     
Smara             III                      III                      
Puerto Ayacucho   II/III                   II                       
Tabou             I                        I/II                     
Kuala Lumpur      I                        I                        

### Hernández Fernández (2003) and Rodentia data {-#rodent}

Let's take a look at how effective this model is with the Rodent data. In his 2003 paper we see 'Appendix 1.2 Rodentia faunal bioclimatic spectra of the localities' which appears to be model training data for predicting climate zones from rodent data only. The rodent data of 13 new locations are given in 'Appendix 3.2 Rodentia faunal bioclimatic spectra of the 13 new localities used for the validation of the bioclimatic model.' So let's see if we can reproduce the rodent analysis. We'll generate a new LDA model using the rodent data from 50 locations as the training set, then predict the locations of the 13 new faunas using only their rodent data. 


```r
hf_2003_appdx_1.2 <- # training set
  read_docx(here::here("data/geb_057_sm_appendix1.2.doc")) %>% 
  docx_extract_tbl() 

hf_2003_appdx_2.2 <- # coefficients 
  read_docx(here::here("data/geb_057_sm_appendix 2.2.doc")) %>% 
  docx_extract_tbl() 

hf_2003_appdx_3.2 <- # 13 new locations
  read_docx(here::here("data/geb_057_sm_appendix 3.2.doc")) %>% 
  docx_extract_tbl() 

kable(hf_2003_appdx_1.2, caption="Rodent data from 50 locations")
```



Table: (\#tab:fh-model3-import)Rodent data from 50 locations

Locality                I        II       II.III   III      IV       V        VI       VII      VIII     IX     
----------------------  -------  -------  -------  -------  -------  -------  -------  -------  -------  -------
Barrow                  0.000    0.000    0.000    0.000    0.000    0.000    6.667    0.000    46.667   46.667 
Port Harrison           0.000    0.000    0.000    0.000    0.000    0.000    22.222   22.222   22.222   33.333 
Malye-Karmaerly         0.000    0.000    0.000    0.000    0.000    0.000    0.000    0.000    25.000   75.000 
Mys Chelyuskin          0.000    0.000    0.000    0.000    0.000    0.000    0.000    0.000    25.000   75.000 
Mys Schmidta            0.000    0.000    0.000    0.000    0.000    0.000    6.667    0.000    46.667   46.667 
Fairbanks               0.000    0.000    1.333    1.333    1.333    3.000    24.333   8.778    51.000   8.889  
Fort Smith              0.000    0.000    2.619    2.619    2.619    3.214    23.690   16.548   48.690   0.000  
Kajaani                 1.010    1.010    1.010    1.010    8.586    3.283    29.798   3.283    47.980   3.030  
Serov                   0.654    0.654    0.654    0.654    5.556    2.124    27.124   7.026    53.595   1.961  
Erbogachen              1.389    1.389    1.389    1.389    8.681    1.389    19.097   4.514    56.597   4.167  
Rapid City              0.000    0.909    6.136    5.227    3.712    4.470    22.955   44.924   11.667   0.000  
Santa Cruz              0.000    0.000    13.542   7.292    7.292    4.167    16.667   51.042   0.000    0.000  
Fort Schevchenko        0.505    0.505    0.505    2.020    10.354   0.505    4.293    80.808   0.505    0.000  
Urumchi                 1.010    1.010    1.010    1.010    5.556    1.010    1.010    87.374   1.010    0.000  
Paotou                  0.529    0.529    0.529    0.529    0.529    0.529    0.529    95.767   0.529    0.000  
Prince Rupert           0.000    0.000    2.821    2.821    4.744    3.462    30.000   18.462   37.692   0.000  
Cleveland               0.000    3.137    3.333    2.157    2.157    13.627   42.255   13.824   19.510   0.000  
Evangelistas            0.000    1.818    11.667   5.303    8.333    7.879    44.242   20.758   0.000    0.000  
Vlissingen              2.116    2.116    2.116    2.116    13.228   4.200    45.172   3.009    23.148   2.778  
Tsingtao                1.389    5.556    1.389    1.389    4.514    8.681    46.181   26.389   4.514    0.000  
New Orleans             1.538    6.667    4.103    4.103    1.538    52.436   20.128   6.026    3.462    0.000  
Puerto Montt            0.000    2.500    5.625    4.167    15.625   55.625   13.958   2.500    0.000    0.000  
Corrientes              7.738    42.500   15.119   0.000    0.000    31.786   1.429    1.429    0.000    0.000  
Foochow                 11.788   28.455   2.965    2.965    2.965    37.278   10.808   0.654    2.124    0.000  
Kagoshima               4.894    2.116    2.116    2.116    4.200    40.311   36.144   0.926    7.176    0.000  
Fresno                  0.000    4.444    8.222    8.222    47.333   1.333    14.000   11.778   4.667    0.000  
Santiago de Chile       0.000    0.000    6.944    7.639    61.111   10.417   11.806   2.083    0.000    0.000  
Sanlucar de Barrameda   2.822    2.822    2.822    2.822    48.192   5.600    25.970   1.235    7.716    0.000  
Aleppo                  1.270    2.270    2.270    7.270    55.603   2.520    17.103   8.639    3.056    0.000  
Esfahan                 0.585    1.637    4.708    16.111   50.760   0.585    8.918    14.795   1.901    0.000  
Phoenix                 0.000    4.808    21.859   41.731   4.231    1.731    6.603    15.897   3.141    0.000  
Arica                   0.000    0.000    25.000   36.111   19.444   11.111   0.000    8.333    0.000    0.000  
Asswan                  3.243    6.814    13.957   37.766   25.266   3.600    3.600    3.175    2.579    0.000  
Mascate                 5.079    5.079    11.746   31.746   31.746   5.079    5.079    2.222    2.222    0.000  
Lüderitz Bay            1.754    8.246    17.018   56.491   12.632   3.860    0.000    0.000    0.000    0.000  
Las Piedras             26.667   26.667   46.667   0.000    0.000    0.000    0.000    0.000    0.000    0.000  
Catamarca               1.471    16.373   62.451   4.902    0.000    7.549    1.176    6.078    0.000    0.000  
Zinder                  3.684    29.123   54.561   6.316    5.439    0.877    0.000    0.000    0.000    0.000  
Voi                     10.889   44.074   24.074   8.630    5.556    6.778    0.000    0.000    0.000    0.000  
Jaipur                  4.841    25.619   36.175   20.619   7.841    0.952    0.952    3.000    0.000    0.000  
Roque Saenz Peña        2.500    49.500   24.500   0.000    0.000    19.500   2.000    2.000    0.000    0.000  
Ziguinchor              21.923   48.846   22.564   2.051    2.051    2.564    0.000    0.000    0.000    0.000  
Moundou                 18.986   43.261   23.696   2.319    4.855    6.884    0.000    0.000    0.000    0.000  
Mtwara                  16.222   52.833   12.833   3.556    5.056    9.500    0.000    0.000    0.000    0.000  
Patna                   6.052    40.357   32.024   12.579   4.940    1.190    1.190    1.667    0.000    0.000  
Tumaco                  74.306   15.972   7.639    0.000    0.000    2.083    0.000    0.000    0.000    0.000  
Paramaribo              68.860   21.491   8.333    0.000    0.000    1.316    0.000    0.000    0.000    0.000  
Kribi                   80.833   13.526   1.667    1.026    1.026    1.923    0.000    0.000    0.000    0.000  
Yangambi                68.295   19.922   3.643    1.628    2.093    4.419    0.000    0.000    0.000    0.000  
Medan                   84.620   11.647   0.386    0.386    0.386    2.188    0.386    0.000    0.000    0.000  

```r
kable(hf_2003_appdx_3.2, caption="Rodent data from 13 locations")
```



Table: (\#tab:fh-model3-import)Rodent data from 13 locations

Rodentia          I        II       II.III   III      IV       V        VI       VII      VIII     IX     
----------------  -------  -------  -------  -------  -------  -------  -------  -------  -------  -------
Ust Kamchatsk     0.000    0.000    0.000    0.000    4.167    0.000    8.333    0.000    52.083   35.417 
Saskatoon         0.000    0.000    2.037    2.037    2.037    5.278    29.537   21.204   37.870   0.000  
Riga              1.494    1.494    1.494    1.494    9.337    2.965    46.592   2.124    31.046   1.961  
Budapest          1.270    1.270    1.270    1.270    13.770   2.520    52.937   11.806   13.889   0.000  
Dairen            1.235    1.235    1.235    1.235    7.716    4.012    46.605   12.346   24.383   0.000  
Portland          0.000    0.000    2.444    2.444    20.778   3.000    49.333   9.333    12.667   0.000  
Charleston        1.667    4.444    1.667    1.667    1.667    54.722   22.500   7.222    4.444    0.000  
Niza              1.494    1.494    1.494    1.494    31.396   2.965    43.161   0.654    15.850   0.000  
Kweilin           17.758   21.925   3.175    3.175    3.175    28.175   19.841   1.389    1.389    0.000  
Smara             2.857    2.857    12.381   45.714   31.429   0.000    0.000    4.762    0.000    0.000  
Puerto Ayacucho   32.778   38.333   21.667   2.222    0.000    5.000    0.000    0.000    0.000    0.000  
Tabou             77.167   17.167   2.583    0.917    0.917    1.250    0.000    0.000    0.000    0.000  
Kuala Lumpur      84.839   10.480   0.651    0.651    0.651    1.506    0.651    0.285    0.285    0.000  

Here we generate a LDA model based on the rodent data from 50 locations, as we see in Appendix 1.2 of the 2003 paper: 


```r
library(caret)
set.seed(998)

# get the actual climate zones, omit NA, and covert chr to dbl
fh_2003_rodent_BCi_values_and_Cl <- 
  left_join(hf_2003_appdx_1.2, 
            fh_2001_tbl_appx_1) %>% 
  dplyr::select(-c(Locality , 
                   Latitude,  
                   Longitude, 
                   Altitude,
                   References,
                   Country,
                   N)) %>% 
  filter(!is.na(Cl.)) %>% 
  mutate_at(vars(1:10), ~parse_double(.))

training3 <- fh_2003_rodent_BCi_values_and_Cl

# estimate pre processing parameters
# center and scale
preProcValues3 <- preProcess(training3, method = c("center", "scale"))

trainTransformed3 <-  
 predict(preProcValues3, training3)

# now run DFA using Linear Discriminant analysis (LDA) on the normalized data 

# Basic Parameter Tuning
fitControl <- trainControl(## 10-fold CV
  method = "repeatedcv",
  number = 10,
  ## repeated ten times
  repeats = 10)

# fit the model to the training data
ldaFit3 <- train(Cl. ~ ., 
                 trControl = fitControl,
                 data = trainTransformed3, 
                 method = "lda")
```

Now we use this rodent LDA model to classify the 13 new locations presented in Appendix 3.2 of the 2003 paper. Once again we see a good classification, all the predictions match up with the actual zones. 


```r
# use this model to predict the classifications of the test data on the 13 new locations
testing3  <- 
  hf_2003_appdx_3.2 %>% 
  select(-Rodentia) %>% 
  mutate_at(vars(1:10), ~parse_double(.))

testTransformed3 <- 
 predict(preProcValues3, testing3)

test_predictions_ldaFit3 <- 
  predict(ldaFit3, 
          newdata = testTransformed3)

# model against the testing set 
kable(tibble(Location = hf_2003_appdx_3.2$Rodentia, 
       `Predicted by our model`= test_predictions_ldaFit3,
       `Actual zone from table 2` = fh_2003_tbl_2_no_na$`Climate zone`), 
      caption = "Model predictions versus actual climate zones for Rodent data from the 13 new locations")
```



Table: (\#tab:fh-model3-predict)Model predictions versus actual climate zones for Rodent data from the 13 new locations

Location          Predicted by our model   Actual zone from table 2 
----------------  -----------------------  -------------------------
Ust Kamchatsk     IX                       VIII/IX                  
Saskatoon         VIII                     VI/VIII                  
Riga              VI                       VI/VIII                  
Budapest          VI                       VI/VII                   
Dairen            VI                       VI                       
Portland          VI                       IV/VI                    
Charleston        V                        V                        
Niza              VI                       IV/VI                    
Kweilin           V                        II/V                     
Smara             III                      III                      
Puerto Ayacucho   II                       II                       
Tabou             I                        I/II                     
Kuala Lumpur      I                        I                        

The coefficients of Hernández Fernández (2003) rodent-only model are presented in 'Appendix 2.2 Coefficients for discriminant functions calculated from rodent faunal bioclimatic components and centroids, for each discriminant function, for every locality's biome group.' We can also show the coefficients from our rodent model. 

Here are Hernández Fernández (2003) rodent LDA coefficients:


```r
kable(hf_2003_appdx_2.2, caption = "Hernández Fernández (2003) rodent-only coefficients")
```



Table: (\#tab:fh-coef)Hernández Fernández (2003) rodent-only coefficients

Coefficients   Func..1   Func..2   Func..3   Func..4   Func..5   Func..6   Func..7   Func..8   Func..9 
-------------  --------  --------  --------  --------  --------  --------  --------  --------  --------
BC I           0.270     -0.003    -0.085    0.082     0.019     0.000     0.091     0.041     -0.011  
BC II          0.109     0.092     0.105     0.047     0.073     -0.017    0.080     0.031     0.092   
BC II/III      0.150     0.112     0.035     0.004     0.091     0.016     0.096     0.051     -0.092  
BC III         0.166     0.142     -0.028    0.017     -0.095    0.116     0.133     0.014     0.046   
BC IV          0.064     0.214     -0.053    0.085     0.030     -0.088    0.046     0.074     0.000   
BC V           0.230     0.025     0.145     0.137     -0.079    0.008     0.049     0.091     -0.032  
BC VI          0.020     0.055     0.024     0.099     0.053     -0.014    0.124     -0.078    0.005   
BC VII         0.051     0.071     -0.005    0.113     0.060     0.064     0.057     0.060     0.007   
BC VIII        0.034     0.030     0.013     0.053     0.015     -0.004    0.158     0.128     0.004   
Constante      -10.749   -7.541    -1.485    -6.660    -2.593    -0.543    -8.725    -4.180    0-.240  

And here are our rodent LDA coefficients, they are very different, and it's not clear why. I can see comparisons of SPSS and R LDA methods online and they can get the same coefficients to within 2 decimal places. 


```r
kable(ldaFit3$finalModel$scaling, , caption = "Our rodent-only coefficients")
```



Table: (\#tab:our-coef)Our rodent-only coefficients

                 LD1          LD2          LD3          LD4          LD5          LD6          LD7          LD8          LD9
-------  -----------  -----------  -----------  -----------  -----------  -----------  -----------  -----------  -----------
I         -3.9456120    1.8571946   -2.2839127   -0.2043386    0.4382413   -0.0972965    0.1883264   -0.0204625   -0.3609093
II        -0.2797960   -0.5126244    1.2611972    0.1723866   -0.4390170   -0.7446459   -0.2413689    0.0081013    1.3871780
II.III    -1.0241057   -0.7294386    0.2318606    0.9442555   -0.7321051   -0.2775421    0.1962765    0.0178003   -1.4095681
III       -0.9695998   -0.6980201   -0.4395415    0.4824274    0.6068351    1.8324211    0.7506079   -0.2322662    0.5570933
IV         0.5882098   -2.2320757   -1.4289889   -0.2194505    0.8020506   -1.0958608   -0.7320807    0.5580960   -0.1203793
V         -1.8408240    0.5208403    1.5959474   -1.0701329    1.1697192    0.6228781   -0.4270331    0.6261641   -0.5096572
VI         0.9474207    0.1252653    0.1378399   -0.1836348    0.1051836   -0.6047633    0.5664057   -1.6890092    0.1075054
VII        1.2723898    0.3318725   -0.6340474   -1.1374079   -1.1453407    0.7336087   -0.5776102    0.2896453   -0.0596415
VIII       1.1380177    0.7122026    0.0084488    0.1163727    0.2443096   -0.1868536    1.3254601    1.6516796   -0.0875825
IX         1.4513957    1.1542276   -0.0763914    1.3956945    0.6375633    0.2992819   -1.5162156   -0.6563265   -0.0502355

Let's try it again without any pre-processing of the data, still our coefficients are not similar to those published by Hernández Fernández (2003)


```r
# now run DFA using Linear Discriminant analysis (LDA) on the normalized data 

# Basic Parameter Tuning
fitControl <- trainControl(## 10-fold CV
  method = "repeatedcv",
  number = 10,
  ## repeated ten times
  repeats = 10)

# fit the model to the training data
ldaFit4 <- train(Cl. ~ ., 
                 trControl = fitControl,
                 data = training3, 
                 method = "lda")

# predict Climate zones of new data

test_predictions_ldaFit4 <- 
  predict(ldaFit4, 
          newdata = testing3)

kable(ldaFit4$finalModel$scaling, , caption = "Our rodent-only coefficients, no transformation to model inputs")
```



Table: (\#tab:unnamed-chunk-1)Our rodent-only coefficients, no transformation to model inputs

                 LD1          LD2          LD3          LD4          LD5          LD6          LD7          LD8          LD9
-------  -----------  -----------  -----------  -----------  -----------  -----------  -----------  -----------  -----------
I         -0.1699448    0.0799928   -0.0983723   -0.0088012    0.0188759   -0.0041907    0.0081116   -0.0008814   -0.0155450
II        -0.0169915   -0.0311308    0.0765903    0.0104687   -0.0266607   -0.0452210   -0.0146579    0.0004920    0.0842409
II.III    -0.0687881   -0.0489957    0.0155738    0.0634247   -0.0491748   -0.0186422    0.0131837    0.0011956   -0.0946793
III       -0.0817828   -0.0588759   -0.0370740    0.0406913    0.0511847    0.1545591    0.0633115   -0.0195909    0.0469891
IV         0.0366497   -0.1390744   -0.0890363   -0.0136733    0.0499735   -0.0682800   -0.0456139    0.0347734   -0.0075005
V         -0.1386407    0.0392268    0.1201979   -0.0805965    0.0880968    0.0469117   -0.0321618    0.0471592   -0.0383846
VI         0.0657054    0.0086874    0.0095595   -0.0127354    0.0072947   -0.0419414    0.0392813   -0.1171359    0.0074557
VII        0.0574823    0.0149929   -0.0286441   -0.0513843   -0.0517426    0.0331420   -0.0260945    0.0130852   -0.0026944
VIII       0.0620679    0.0388438    0.0004608    0.0063470    0.0133247   -0.0101911    0.0722911    0.0900832   -0.0047768
IX         0.0976815    0.0776816   -0.0051413    0.0939327    0.0429091    0.0201422   -0.1020440   -0.0441719   -0.0033809



#### References {-#refs}

Fernández, M. H. (2001). Bioclimatic discriminant capacity of terrestrial mammal faunas. Global Ecology and Biogeography, 10(2), 189-204. <https://doi.org/10.1046/j.1466-822x.2001.00218.x>

Fernández, M. H., & Peláez‐Campomanes, P. (2003). The bioclimatic model: a method of palaeoclimatic qualitative inference based on mammal associations. _Global Ecology and Biogeography_, 12(6), 507-517. <https://doi.org/10.1046/j.1466-822X.2003.00057.x>


```r
sessioninfo::session_info()
```

```
## ─ Session info ──────────────────────────────────────────────────────────
##  setting  value                       
##  version  R version 3.6.0 (2019-04-26)
##  os       macOS Mojave 10.14.5        
##  system   x86_64, darwin15.6.0        
##  ui       X11                         
##  language (EN)                        
##  collate  en_US.UTF-8                 
##  ctype    en_US.UTF-8                 
##  tz       America/Los_Angeles         
##  date     2019-06-29                  
## 
## ─ Packages ──────────────────────────────────────────────────────────────
##  package      * version    date       lib
##  assertthat     0.2.1      2019-03-21 [1]
##  backports      1.1.4      2019-04-10 [1]
##  bookdown       0.11       2019-05-28 [1]
##  broom          0.5.2      2019-04-07 [1]
##  caret        * 6.0-84     2019-04-27 [1]
##  cellranger     1.1.0.9000 2019-05-28 [1]
##  class          7.3-15     2019-01-01 [1]
##  cli            1.1.0      2019-03-19 [1]
##  codetools      0.2-16     2018-12-24 [1]
##  colorspace     1.4-1      2019-03-18 [1]
##  crayon         1.3.4      2019-05-28 [1]
##  data.table     1.12.2     2019-04-07 [1]
##  digest         0.6.19     2019-05-20 [1]
##  docxtractr   * 0.6.1      2019-01-09 [1]
##  dplyr        * 0.8.1      2019-05-14 [1]
##  e1071          1.7-2      2019-06-05 [1]
##  evaluate       0.14       2019-05-28 [1]
##  forcats      * 0.4.0      2019-02-17 [1]
##  foreach        1.4.4      2017-12-12 [1]
##  generics       0.0.2      2018-11-29 [1]
##  ggplot2      * 3.2.0      2019-06-16 [1]
##  glue           1.3.1      2019-03-12 [1]
##  gower          0.2.1      2019-05-14 [1]
##  gtable         0.3.0      2019-03-25 [1]
##  haven          2.1.0      2019-02-19 [1]
##  here           0.1        2017-05-28 [1]
##  highr          0.8        2019-03-20 [1]
##  hms            0.4.2      2018-03-10 [1]
##  htmltools      0.3.6      2017-04-28 [1]
##  httr           1.4.0      2018-12-11 [1]
##  ipred          0.9-9      2019-04-28 [1]
##  iterators      1.0.10     2018-07-13 [1]
##  jsonlite       1.6        2018-12-07 [1]
##  knitr        * 1.23       2019-05-18 [1]
##  lattice      * 0.20-38    2018-11-04 [1]
##  lava           1.6.5      2019-02-12 [1]
##  lazyeval       0.2.2      2019-03-15 [1]
##  lubridate      1.7.4      2018-04-11 [1]
##  magrittr       1.5        2014-11-22 [1]
##  MASS           7.3-51.4   2019-03-31 [1]
##  Matrix         1.2-17     2019-03-22 [1]
##  ModelMetrics   1.2.2      2018-11-03 [1]
##  modelr         0.1.4      2019-02-18 [1]
##  munsell        0.5.0      2018-06-12 [1]
##  nlme           3.1-140    2019-05-12 [1]
##  nnet           7.3-12     2016-02-02 [1]
##  pillar         1.4.1      2019-05-28 [1]
##  pkgconfig      2.0.2      2018-08-16 [1]
##  plyr           1.8.4      2016-06-08 [1]
##  prodlim        2018.04.18 2018-04-18 [1]
##  purrr        * 0.3.2      2019-03-15 [1]
##  R6             2.4.0      2019-02-14 [1]
##  Rcpp           1.0.1      2019-03-17 [1]
##  readr        * 1.3.1      2018-12-21 [1]
##  readxl         1.3.1      2019-03-13 [1]
##  recipes        0.1.5      2019-03-21 [1]
##  reshape2       1.4.3      2017-12-11 [1]
##  rlang          0.4.0      2019-06-25 [1]
##  rmarkdown      1.13       2019-05-22 [1]
##  rpart          4.1-15     2019-04-12 [1]
##  rprojroot      1.3-2      2018-01-03 [1]
##  rstudioapi     0.10       2019-03-19 [1]
##  rvest          0.3.4      2019-05-15 [1]
##  scales         1.0.0      2018-08-09 [1]
##  sessioninfo    1.1.1      2018-11-05 [1]
##  stringi        1.4.3      2019-03-12 [1]
##  stringr      * 1.4.0      2019-02-10 [1]
##  survival       2.44-1.1   2019-04-01 [1]
##  tibble       * 2.1.3      2019-06-06 [1]
##  tidyr        * 0.8.3      2019-03-01 [1]
##  tidyselect     0.2.5      2018-10-11 [1]
##  tidyverse    * 1.2.1      2017-11-14 [1]
##  timeDate       3043.102   2018-02-21 [1]
##  withr          2.1.2      2018-03-15 [1]
##  xfun           0.8        2019-06-25 [1]
##  xml2           1.2.0.9000 2019-06-03 [1]
##  yaml           2.2.0      2018-07-25 [1]
##  source                             
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  Github (rsheets/cellranger@7ecde54)
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  Github (gaborcsardi/crayon@84be620)
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  CRAN (R 3.6.0)                     
##  Github (hadley/xml2@e051ffa)       
##  CRAN (R 3.6.0)                     
## 
## [1] /Library/Frameworks/R.framework/Versions/3.6/Resources/library
```

