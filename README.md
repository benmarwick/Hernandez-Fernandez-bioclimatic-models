Partial reproduction of the LDA models and predictions for mammal
bioclimatic models from Hernández Fernández 2001 & 2003
================
Ben Marwick and Gillian Wong
29 June, 2019

<!-- badges: start -->

[![Launch Rstudio
binder](http://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/benmarwick/Hernandez-Fernandez-bioclimatic-models/master?urlpath=rstudio)
<!-- badges: end -->

### Hernández Fernández (2001) and the whole mammal bioclimatic model

Hernández Fernández (2001, 2003) proposed a bioclimatic model for
palaeoclimatic reconstruction based on the assumption of a correlation
between climate and mammal community composition.

In his 2001 paper we see table 2 which has data for whole mammal faunal
bioclimatic spectra of the localities. Each column represents a
bioclimatic component, and BCi values. From Appendix 1 of the 2001 paper
we can get the actual climatic zones for the localities in table 2.
Using these two tables we can make a labelled training set to train a
machine learning model that can predict the climate zone of new faunal
assemblages.

``` r
library(docxtractr)
library(tidyverse)
library(kableExtra)
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
(kable(fh_2001_BCi_values_and_Cl, caption = "Whole mammal faunal bioclimatic spectra from 50 locations"))
```

<table>

<caption>

Whole mammal faunal bioclimatic spectra from 50 locations

</caption>

<thead>

<tr>

<th style="text-align:left;">

Locality

</th>

<th style="text-align:right;">

I

</th>

<th style="text-align:right;">

II

</th>

<th style="text-align:right;">

II/III

</th>

<th style="text-align:right;">

III

</th>

<th style="text-align:right;">

IV

</th>

<th style="text-align:right;">

V

</th>

<th style="text-align:right;">

VI

</th>

<th style="text-align:right;">

VII

</th>

<th style="text-align:right;">

VIII

</th>

<th style="text-align:right;">

IX

</th>

<th style="text-align:left;">

Cl.

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Barrow

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

1.209

</td>

<td style="text-align:right;">

1.209

</td>

<td style="text-align:right;">

1.890

</td>

<td style="text-align:right;">

3.794

</td>

<td style="text-align:right;">

2.162

</td>

<td style="text-align:right;">

10.540

</td>

<td style="text-align:right;">

7.366

</td>

<td style="text-align:right;">

31.969

</td>

<td style="text-align:right;">

39.860

</td>

<td style="text-align:left;">

IX

</td>

</tr>

<tr>

<td style="text-align:left;">

Port Harrison

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.794

</td>

<td style="text-align:right;">

0.794

</td>

<td style="text-align:right;">

1.814

</td>

<td style="text-align:right;">

4.671

</td>

<td style="text-align:right;">

3.243

</td>

<td style="text-align:right;">

15.385

</td>

<td style="text-align:right;">

13.005

</td>

<td style="text-align:right;">

22.528

</td>

<td style="text-align:right;">

37.766

</td>

<td style="text-align:left;">

IX

</td>

</tr>

<tr>

<td style="text-align:left;">

Malye‐Karmaerly

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

1.587

</td>

<td style="text-align:right;">

1.587

</td>

<td style="text-align:right;">

3.628

</td>

<td style="text-align:right;">

3.628

</td>

<td style="text-align:right;">

3.628

</td>

<td style="text-align:right;">

3.628

</td>

<td style="text-align:right;">

3.628

</td>

<td style="text-align:right;">

25.057

</td>

<td style="text-align:right;">

53.628

</td>

<td style="text-align:left;">

IX

</td>

</tr>

<tr>

<td style="text-align:left;">

Mys Chelyuskin

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

1.235

</td>

<td style="text-align:right;">

1.235

</td>

<td style="text-align:right;">

1.235

</td>

<td style="text-align:right;">

1.235

</td>

<td style="text-align:right;">

1.235

</td>

<td style="text-align:right;">

4.938

</td>

<td style="text-align:right;">

1.235

</td>

<td style="text-align:right;">

32.716

</td>

<td style="text-align:right;">

54.938

</td>

<td style="text-align:left;">

IX

</td>

</tr>

<tr>

<td style="text-align:left;">

Mys Schmidta

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.694

</td>

<td style="text-align:right;">

0.694

</td>

<td style="text-align:right;">

0.694

</td>

<td style="text-align:right;">

1.944

</td>

<td style="text-align:right;">

0.694

</td>

<td style="text-align:right;">

7.674

</td>

<td style="text-align:right;">

3.507

</td>

<td style="text-align:right;">

42.049

</td>

<td style="text-align:right;">

42.049

</td>

<td style="text-align:left;">

IX

</td>

</tr>

<tr>

<td style="text-align:left;">

Fairbanks

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.705

</td>

<td style="text-align:right;">

2.584

</td>

<td style="text-align:right;">

2.055

</td>

<td style="text-align:right;">

4.416

</td>

<td style="text-align:right;">

3.463

</td>

<td style="text-align:right;">

22.101

</td>

<td style="text-align:right;">

9.601

</td>

<td style="text-align:right;">

45.712

</td>

<td style="text-align:right;">

9.363

</td>

<td style="text-align:left;">

VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Fort Smith

</td>

<td style="text-align:right;">

0.605

</td>

<td style="text-align:right;">

1.209

</td>

<td style="text-align:right;">

4.293

</td>

<td style="text-align:right;">

3.840

</td>

<td style="text-align:right;">

5.523

</td>

<td style="text-align:right;">

4.849

</td>

<td style="text-align:right;">

22.808

</td>

<td style="text-align:right;">

12.887

</td>

<td style="text-align:right;">

37.944

</td>

<td style="text-align:right;">

6.041

</td>

<td style="text-align:left;">

VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Kajaani

</td>

<td style="text-align:right;">

0.726

</td>

<td style="text-align:right;">

1.043

</td>

<td style="text-align:right;">

0.635

</td>

<td style="text-align:right;">

1.043

</td>

<td style="text-align:right;">

8.166

</td>

<td style="text-align:right;">

2.737

</td>

<td style="text-align:right;">

25.546

</td>

<td style="text-align:right;">

9.594

</td>

<td style="text-align:right;">

41.737

</td>

<td style="text-align:right;">

8.773

</td>

<td style="text-align:left;">

VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Serov

</td>

<td style="text-align:right;">

0.518

</td>

<td style="text-align:right;">

0.745

</td>

<td style="text-align:right;">

0.454

</td>

<td style="text-align:right;">

0.745

</td>

<td style="text-align:right;">

7.873

</td>

<td style="text-align:right;">

2.465

</td>

<td style="text-align:right;">

26.241

</td>

<td style="text-align:right;">

10.764

</td>

<td style="text-align:right;">

43.928

</td>

<td style="text-align:right;">

6.267

</td>

<td style="text-align:left;">

VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Erbogachen

</td>

<td style="text-align:right;">

0.876

</td>

<td style="text-align:right;">

1.259

</td>

<td style="text-align:right;">

0.766

</td>

<td style="text-align:right;">

0.766

</td>

<td style="text-align:right;">

4.822

</td>

<td style="text-align:right;">

2.121

</td>

<td style="text-align:right;">

12.293

</td>

<td style="text-align:right;">

7.121

</td>

<td style="text-align:right;">

55.397

</td>

<td style="text-align:right;">

14.579

</td>

<td style="text-align:left;">

VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Rapid City

</td>

<td style="text-align:right;">

1.139

</td>

<td style="text-align:right;">

3.505

</td>

<td style="text-align:right;">

8.216

</td>

<td style="text-align:right;">

8.641

</td>

<td style="text-align:right;">

9.934

</td>

<td style="text-align:right;">

6.575

</td>

<td style="text-align:right;">

19.718

</td>

<td style="text-align:right;">

31.769

</td>

<td style="text-align:right;">

8.907

</td>

<td style="text-align:right;">

1.596

</td>

<td style="text-align:left;">

VII

</td>

</tr>

<tr>

<td style="text-align:left;">

Puerto Santa Cruz

</td>

<td style="text-align:right;">

2.105

</td>

<td style="text-align:right;">

4.142

</td>

<td style="text-align:right;">

14.050

</td>

<td style="text-align:right;">

7.661

</td>

<td style="text-align:right;">

9.883

</td>

<td style="text-align:right;">

11.272

</td>

<td style="text-align:right;">

15.849

</td>

<td style="text-align:right;">

34.420

</td>

<td style="text-align:right;">

0.617

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

VII

</td>

</tr>

<tr>

<td style="text-align:left;">

Fort Schevchenko

</td>

<td style="text-align:right;">

1.675

</td>

<td style="text-align:right;">

2.215

</td>

<td style="text-align:right;">

1.506

</td>

<td style="text-align:right;">

5.052

</td>

<td style="text-align:right;">

16.491

</td>

<td style="text-align:right;">

2.342

</td>

<td style="text-align:right;">

8.619

</td>

<td style="text-align:right;">

58.973

</td>

<td style="text-align:right;">

2.160

</td>

<td style="text-align:right;">

0.966

</td>

<td style="text-align:left;">

VII

</td>

</tr>

<tr>

<td style="text-align:left;">

Urumchi

</td>

<td style="text-align:right;">

0.926

</td>

<td style="text-align:right;">

1.296

</td>

<td style="text-align:right;">

1.296

</td>

<td style="text-align:right;">

2.963

</td>

<td style="text-align:right;">

9.574

</td>

<td style="text-align:right;">

1.963

</td>

<td style="text-align:right;">

8.741

</td>

<td style="text-align:right;">

65.963

</td>

<td style="text-align:right;">

5.407

</td>

<td style="text-align:right;">

1.870

</td>

<td style="text-align:left;">

VII

</td>

</tr>

<tr>

<td style="text-align:left;">

Paotou

</td>

<td style="text-align:right;">

0.236

</td>

<td style="text-align:right;">

0.777

</td>

<td style="text-align:right;">

1.202

</td>

<td style="text-align:right;">

2.925

</td>

<td style="text-align:right;">

7.322

</td>

<td style="text-align:right;">

2.038

</td>

<td style="text-align:right;">

8.386

</td>

<td style="text-align:right;">

72.570

</td>

<td style="text-align:right;">

3.578

</td>

<td style="text-align:right;">

0.966

</td>

<td style="text-align:left;">

VII

</td>

</tr>

<tr>

<td style="text-align:left;">

Prince Rupert

</td>

<td style="text-align:right;">

0.309

</td>

<td style="text-align:right;">

1.080

</td>

<td style="text-align:right;">

4.837

</td>

<td style="text-align:right;">

3.911

</td>

<td style="text-align:right;">

6.411

</td>

<td style="text-align:right;">

4.698

</td>

<td style="text-align:right;">

28.726

</td>

<td style="text-align:right;">

15.763

</td>

<td style="text-align:right;">

29.559

</td>

<td style="text-align:right;">

4.707

</td>

<td style="text-align:left;">

VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Cleveland

</td>

<td style="text-align:right;">

1.858

</td>

<td style="text-align:right;">

4.685

</td>

<td style="text-align:right;">

5.093

</td>

<td style="text-align:right;">

4.665

</td>

<td style="text-align:right;">

5.982

</td>

<td style="text-align:right;">

11.677

</td>

<td style="text-align:right;">

33.470

</td>

<td style="text-align:right;">

14.304

</td>

<td style="text-align:right;">

16.121

</td>

<td style="text-align:right;">

2.146

</td>

<td style="text-align:left;">

VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Evangelistas

</td>

<td style="text-align:right;">

1.458

</td>

<td style="text-align:right;">

4.887

</td>

<td style="text-align:right;">

11.554

</td>

<td style="text-align:right;">

8.284

</td>

<td style="text-align:right;">

11.169

</td>

<td style="text-align:right;">

12.900

</td>

<td style="text-align:right;">

30.876

</td>

<td style="text-align:right;">

17.964

</td>

<td style="text-align:right;">

0.908

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Vlissingen

</td>

<td style="text-align:right;">

0.968

</td>

<td style="text-align:right;">

1.587

</td>

<td style="text-align:right;">

1.239

</td>

<td style="text-align:right;">

1.587

</td>

<td style="text-align:right;">

20.066

</td>

<td style="text-align:right;">

3.643

</td>

<td style="text-align:right;">

41.814

</td>

<td style="text-align:right;">

10.368

</td>

<td style="text-align:right;">

15.710

</td>

<td style="text-align:right;">

3.018

</td>

<td style="text-align:left;">

VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Tsingtao

</td>

<td style="text-align:right;">

5.474

</td>

<td style="text-align:right;">

6.821

</td>

<td style="text-align:right;">

3.862

</td>

<td style="text-align:right;">

1.539

</td>

<td style="text-align:right;">

8.314

</td>

<td style="text-align:right;">

16.193

</td>

<td style="text-align:right;">

37.658

</td>

<td style="text-align:right;">

14.952

</td>

<td style="text-align:right;">

4.418

</td>

<td style="text-align:right;">

0.770

</td>

<td style="text-align:left;">

VI

</td>

</tr>

<tr>

<td style="text-align:left;">

New Orleans

</td>

<td style="text-align:right;">

5.451

</td>

<td style="text-align:right;">

9.355

</td>

<td style="text-align:right;">

6.650

</td>

<td style="text-align:right;">

5.194

</td>

<td style="text-align:right;">

5.021

</td>

<td style="text-align:right;">

37.883

</td>

<td style="text-align:right;">

17.542

</td>

<td style="text-align:right;">

8.641

</td>

<td style="text-align:right;">

3.938

</td>

<td style="text-align:right;">

0.325

</td>

<td style="text-align:left;">

V

</td>

</tr>

<tr>

<td style="text-align:left;">

Puerto Montt

</td>

<td style="text-align:right;">

1.723

</td>

<td style="text-align:right;">

5.245

</td>

<td style="text-align:right;">

7.291

</td>

<td style="text-align:right;">

4.942

</td>

<td style="text-align:right;">

17.367

</td>

<td style="text-align:right;">

42.518

</td>

<td style="text-align:right;">

13.308

</td>

<td style="text-align:right;">

6.533

</td>

<td style="text-align:right;">

1.073

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

V

</td>

</tr>

<tr>

<td style="text-align:left;">

Corrientes

</td>

<td style="text-align:right;">

17.391

</td>

<td style="text-align:right;">

32.869

</td>

<td style="text-align:right;">

18.115

</td>

<td style="text-align:right;">

1.612

</td>

<td style="text-align:right;">

1.694

</td>

<td style="text-align:right;">

25.219

</td>

<td style="text-align:right;">

1.284

</td>

<td style="text-align:right;">

1.612

</td>

<td style="text-align:right;">

0.205

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

V

</td>

</tr>

<tr>

<td style="text-align:left;">

Foochow

</td>

<td style="text-align:right;">

16.255

</td>

<td style="text-align:right;">

22.343

</td>

<td style="text-align:right;">

5.595

</td>

<td style="text-align:right;">

1.038

</td>

<td style="text-align:right;">

3.873

</td>

<td style="text-align:right;">

33.804

</td>

<td style="text-align:right;">

12.685

</td>

<td style="text-align:right;">

2.340

</td>

<td style="text-align:right;">

1.916

</td>

<td style="text-align:right;">

0.152

</td>

<td style="text-align:left;">

V

</td>

</tr>

<tr>

<td style="text-align:left;">

Kagoshima

</td>

<td style="text-align:right;">

5.659

</td>

<td style="text-align:right;">

6.770

</td>

<td style="text-align:right;">

2.663

</td>

<td style="text-align:right;">

1.687

</td>

<td style="text-align:right;">

4.627

</td>

<td style="text-align:right;">

36.085

</td>

<td style="text-align:right;">

34.085

</td>

<td style="text-align:right;">

3.020

</td>

<td style="text-align:right;">

4.270

</td>

<td style="text-align:right;">

1.130

</td>

<td style="text-align:left;">

V

</td>

</tr>

<tr>

<td style="text-align:left;">

Fresno

</td>

<td style="text-align:right;">

2.016

</td>

<td style="text-align:right;">

5.462

</td>

<td style="text-align:right;">

11.794

</td>

<td style="text-align:right;">

12.429

</td>

<td style="text-align:right;">

29.435

</td>

<td style="text-align:right;">

4.620

</td>

<td style="text-align:right;">

13.306

</td>

<td style="text-align:right;">

13.632

</td>

<td style="text-align:right;">

6.423

</td>

<td style="text-align:right;">

0.882

</td>

<td style="text-align:left;">

IV

</td>

</tr>

<tr>

<td style="text-align:left;">

Santiago de Chile

</td>

<td style="text-align:right;">

2.298

</td>

<td style="text-align:right;">

4.690

</td>

<td style="text-align:right;">

12.432

</td>

<td style="text-align:right;">

12.217

</td>

<td style="text-align:right;">

37.701

</td>

<td style="text-align:right;">

13.131

</td>

<td style="text-align:right;">

10.251

</td>

<td style="text-align:right;">

6.518

</td>

<td style="text-align:right;">

0.762

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

IV

</td>

</tr>

<tr>

<td style="text-align:left;">

Sanlucar de Barrameda

</td>

<td style="text-align:right;">

2.310

</td>

<td style="text-align:right;">

4.430

</td>

<td style="text-align:right;">

3.372

</td>

<td style="text-align:right;">

4.113

</td>

<td style="text-align:right;">

39.896

</td>

<td style="text-align:right;">

6.303

</td>

<td style="text-align:right;">

24.266

</td>

<td style="text-align:right;">

9.690

</td>

<td style="text-align:right;">

4.610

</td>

<td style="text-align:right;">

1.009

</td>

<td style="text-align:left;">

IV

</td>

</tr>

<tr>

<td style="text-align:left;">

Aleppo

</td>

<td style="text-align:right;">

2.026

</td>

<td style="text-align:right;">

5.589

</td>

<td style="text-align:right;">

6.517

</td>

<td style="text-align:right;">

8.739

</td>

<td style="text-align:right;">

41.271

</td>

<td style="text-align:right;">

5.354

</td>

<td style="text-align:right;">

15.299

</td>

<td style="text-align:right;">

11.684

</td>

<td style="text-align:right;">

2.763

</td>

<td style="text-align:right;">

0.757

</td>

<td style="text-align:left;">

IV

</td>

</tr>

<tr>

<td style="text-align:left;">

Esfahan

</td>

<td style="text-align:right;">

0.577

</td>

<td style="text-align:right;">

3.276

</td>

<td style="text-align:right;">

4.601

</td>

<td style="text-align:right;">

10.608

</td>

<td style="text-align:right;">

44.282

</td>

<td style="text-align:right;">

3.979

</td>

<td style="text-align:right;">

11.668

</td>

<td style="text-align:right;">

16.912

</td>

<td style="text-align:right;">

3.065

</td>

<td style="text-align:right;">

1.032

</td>

<td style="text-align:left;">

IV

</td>

</tr>

<tr>

<td style="text-align:left;">

Phoenix

</td>

<td style="text-align:right;">

2.772

</td>

<td style="text-align:right;">

9.240

</td>

<td style="text-align:right;">

21.504

</td>

<td style="text-align:right;">

27.371

</td>

<td style="text-align:right;">

10.326

</td>

<td style="text-align:right;">

4.266

</td>

<td style="text-align:right;">

7.427

</td>

<td style="text-align:right;">

13.490

</td>

<td style="text-align:right;">

3.452

</td>

<td style="text-align:right;">

0.152

</td>

<td style="text-align:left;">

III

</td>

</tr>

<tr>

<td style="text-align:left;">

Arica

</td>

<td style="text-align:right;">

5.177

</td>

<td style="text-align:right;">

5.177

</td>

<td style="text-align:right;">

15.025

</td>

<td style="text-align:right;">

40.783

</td>

<td style="text-align:right;">

18.056

</td>

<td style="text-align:right;">

8.207

</td>

<td style="text-align:right;">

2.146

</td>

<td style="text-align:right;">

4.419

</td>

<td style="text-align:right;">

1.010

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

III

</td>

</tr>

<tr>

<td style="text-align:left;">

Assuan

</td>

<td style="text-align:right;">

2.676

</td>

<td style="text-align:right;">

13.027

</td>

<td style="text-align:right;">

16.360

</td>

<td style="text-align:right;">

37.127

</td>

<td style="text-align:right;">

17.451

</td>

<td style="text-align:right;">

5.831

</td>

<td style="text-align:right;">

3.146

</td>

<td style="text-align:right;">

2.584

</td>

<td style="text-align:right;">

1.400

</td>

<td style="text-align:right;">

0.397

</td>

<td style="text-align:left;">

III

</td>

</tr>

<tr>

<td style="text-align:left;">

Mascate

</td>

<td style="text-align:right;">

2.450

</td>

<td style="text-align:right;">

11.919

</td>

<td style="text-align:right;">

13.368

</td>

<td style="text-align:right;">

34.279

</td>

<td style="text-align:right;">

22.685

</td>

<td style="text-align:right;">

5.293

</td>

<td style="text-align:right;">

2.830

</td>

<td style="text-align:right;">

4.486

</td>

<td style="text-align:right;">

1.587

</td>

<td style="text-align:right;">

1.104

</td>

<td style="text-align:left;">

III

</td>

</tr>

<tr>

<td style="text-align:left;">

Lüderitz Bay

</td>

<td style="text-align:right;">

5.537

</td>

<td style="text-align:right;">

13.146

</td>

<td style="text-align:right;">

21.003

</td>

<td style="text-align:right;">

37.146

</td>

<td style="text-align:right;">

14.527

</td>

<td style="text-align:right;">

8.027

</td>

<td style="text-align:right;">

0.408

</td>

<td style="text-align:right;">

0.204

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

III

</td>

</tr>

<tr>

<td style="text-align:left;">

Las Piedras

</td>

<td style="text-align:right;">

30.769

</td>

<td style="text-align:right;">

32.491

</td>

<td style="text-align:right;">

28.788

</td>

<td style="text-align:right;">

1.170

</td>

<td style="text-align:right;">

0.768

</td>

<td style="text-align:right;">

4.541

</td>

<td style="text-align:right;">

0.521

</td>

<td style="text-align:right;">

0.775

</td>

<td style="text-align:right;">

0.175

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II/III

</td>

</tr>

<tr>

<td style="text-align:left;">

Catamarca

</td>

<td style="text-align:right;">

7.615

</td>

<td style="text-align:right;">

18.742

</td>

<td style="text-align:right;">

44.514

</td>

<td style="text-align:right;">

5.594

</td>

<td style="text-align:right;">

3.773

</td>

<td style="text-align:right;">

11.581

</td>

<td style="text-align:right;">

2.088

</td>

<td style="text-align:right;">

5.655

</td>

<td style="text-align:right;">

0.437

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II/III

</td>

</tr>

<tr>

<td style="text-align:left;">

Zinder

</td>

<td style="text-align:right;">

6.485

</td>

<td style="text-align:right;">

27.304

</td>

<td style="text-align:right;">

37.145

</td>

<td style="text-align:right;">

12.637

</td>

<td style="text-align:right;">

7.510

</td>

<td style="text-align:right;">

8.510

</td>

<td style="text-align:right;">

0.272

</td>

<td style="text-align:right;">

0.136

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II/III

</td>

</tr>

<tr>

<td style="text-align:left;">

Voi

</td>

<td style="text-align:right;">

8.378

</td>

<td style="text-align:right;">

36.424

</td>

<td style="text-align:right;">

29.066

</td>

<td style="text-align:right;">

10.615

</td>

<td style="text-align:right;">

6.434

</td>

<td style="text-align:right;">

8.832

</td>

<td style="text-align:right;">

0.167

</td>

<td style="text-align:right;">

0.084

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II/III

</td>

</tr>

<tr>

<td style="text-align:left;">

Jaipur

</td>

<td style="text-align:right;">

14.233

</td>

<td style="text-align:right;">

29.296

</td>

<td style="text-align:right;">

33.831

</td>

<td style="text-align:right;">

10.802

</td>

<td style="text-align:right;">

5.130

</td>

<td style="text-align:right;">

3.904

</td>

<td style="text-align:right;">

1.723

</td>

<td style="text-align:right;">

1.082

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II/III

</td>

</tr>

<tr>

<td style="text-align:left;">

Roque Saenz Peña

</td>

<td style="text-align:right;">

15.817

</td>

<td style="text-align:right;">

33.237

</td>

<td style="text-align:right;">

24.583

</td>

<td style="text-align:right;">

2.660

</td>

<td style="text-align:right;">

1.891

</td>

<td style="text-align:right;">

18.686

</td>

<td style="text-align:right;">

1.250

</td>

<td style="text-align:right;">

1.635

</td>

<td style="text-align:right;">

0.240

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II

</td>

</tr>

<tr>

<td style="text-align:left;">

Ziguinchor

</td>

<td style="text-align:right;">

16.192

</td>

<td style="text-align:right;">

39.272

</td>

<td style="text-align:right;">

21.434

</td>

<td style="text-align:right;">

8.928

</td>

<td style="text-align:right;">

5.980

</td>

<td style="text-align:right;">

7.839

</td>

<td style="text-align:right;">

0.236

</td>

<td style="text-align:right;">

0.118

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II

</td>

</tr>

<tr>

<td style="text-align:left;">

Moundou

</td>

<td style="text-align:right;">

11.124

</td>

<td style="text-align:right;">

39.671

</td>

<td style="text-align:right;">

24.726

</td>

<td style="text-align:right;">

8.432

</td>

<td style="text-align:right;">

6.379

</td>

<td style="text-align:right;">

9.313

</td>

<td style="text-align:right;">

0.236

</td>

<td style="text-align:right;">

0.118

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II

</td>

</tr>

<tr>

<td style="text-align:left;">

Mtwara

</td>

<td style="text-align:right;">

11.150

</td>

<td style="text-align:right;">

46.505

</td>

<td style="text-align:right;">

19.326

</td>

<td style="text-align:right;">

7.148

</td>

<td style="text-align:right;">

5.198

</td>

<td style="text-align:right;">

10.394

</td>

<td style="text-align:right;">

0.187

</td>

<td style="text-align:right;">

0.093

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II

</td>

</tr>

<tr>

<td style="text-align:left;">

Patna

</td>

<td style="text-align:right;">

17.398

</td>

<td style="text-align:right;">

38.922

</td>

<td style="text-align:right;">

28.207

</td>

<td style="text-align:right;">

6.551

</td>

<td style="text-align:right;">

3.660

</td>

<td style="text-align:right;">

3.303

</td>

<td style="text-align:right;">

1.469

</td>

<td style="text-align:right;">

0.490

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

II

</td>

</tr>

<tr>

<td style="text-align:left;">

Tumaco

</td>

<td style="text-align:right;">

44.932

</td>

<td style="text-align:right;">

28.115

</td>

<td style="text-align:right;">

19.003

</td>

<td style="text-align:right;">

1.137

</td>

<td style="text-align:right;">

0.746

</td>

<td style="text-align:right;">

4.926

</td>

<td style="text-align:right;">

0.363

</td>

<td style="text-align:right;">

0.609

</td>

<td style="text-align:right;">

0.170

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

I

</td>

</tr>

<tr>

<td style="text-align:left;">

Paramaribo

</td>

<td style="text-align:right;">

46.245

</td>

<td style="text-align:right;">

29.179

</td>

<td style="text-align:right;">

18.401

</td>

<td style="text-align:right;">

0.686

</td>

<td style="text-align:right;">

0.361

</td>

<td style="text-align:right;">

4.488

</td>

<td style="text-align:right;">

0.227

</td>

<td style="text-align:right;">

0.347

</td>

<td style="text-align:right;">

0.067

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

I

</td>

</tr>

<tr>

<td style="text-align:left;">

Kribi

</td>

<td style="text-align:right;">

69.971

</td>

<td style="text-align:right;">

17.420

</td>

<td style="text-align:right;">

5.558

</td>

<td style="text-align:right;">

2.360

</td>

<td style="text-align:right;">

1.799

</td>

<td style="text-align:right;">

2.819

</td>

<td style="text-align:right;">

0.073

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

I

</td>

</tr>

<tr>

<td style="text-align:left;">

Yangambi

</td>

<td style="text-align:right;">

65.496

</td>

<td style="text-align:right;">

19.607

</td>

<td style="text-align:right;">

5.950

</td>

<td style="text-align:right;">

2.774

</td>

<td style="text-align:right;">

2.274

</td>

<td style="text-align:right;">

3.820

</td>

<td style="text-align:right;">

0.079

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

I

</td>

</tr>

<tr>

<td style="text-align:left;">

Medan

</td>

<td style="text-align:right;">

69.883

</td>

<td style="text-align:right;">

20.111

</td>

<td style="text-align:right;">

5.002

</td>

<td style="text-align:right;">

0.612

</td>

<td style="text-align:right;">

0.522

</td>

<td style="text-align:right;">

2.588

</td>

<td style="text-align:right;">

0.990

</td>

<td style="text-align:right;">

0.196

</td>

<td style="text-align:right;">

0.098

</td>

<td style="text-align:right;">

0.000

</td>

<td style="text-align:left;">

I

</td>

</tr>

</tbody>

</table>

Using these data, we can roughly follow Hernández Fernández’s method to
compute a Linear Discriminant Analysis to generate a model to predict
the climate zone of a new faunal assemblage:

``` r
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

We can inspect the performance of this model on the training set, and we
can see one location mis-classified

``` r
# model against the training set 
lda_train <- predict(MASS::lda(Cl. ~ ., data = trainTransformed1))
trainTransformed1$lda <- lda_train$class
kable(table(trainTransformed1$lda, trainTransformed1$Cl.),
      caption = "Model performance with training data")
```

<table>

<caption>

Model performance with training data

</caption>

<thead>

<tr>

<th style="text-align:left;">

</th>

<th style="text-align:right;">

I

</th>

<th style="text-align:right;">

II

</th>

<th style="text-align:right;">

II/III

</th>

<th style="text-align:right;">

III

</th>

<th style="text-align:right;">

IV

</th>

<th style="text-align:right;">

IX

</th>

<th style="text-align:right;">

V

</th>

<th style="text-align:right;">

VI

</th>

<th style="text-align:right;">

VII

</th>

<th style="text-align:right;">

VIII

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

I

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

II

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

II/III

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

3

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

III

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

IV

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

IX

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

V

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

VI

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

VII

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

VIII

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

4

</td>

</tr>

</tbody>

</table>

And we can inspect the model on the testing data set, and we see less
accuracy:

``` r
# use this model to predict the classifications of the test data
test_predictions1 <- 
  predict(ldaFit1, 
          newdata = testTransformed1)
# model against the testing set 
kable(table(test_predictions1, testTransformed1$Cl.),
      caption = "Model performance with hold-out test data")
```

<table>

<caption>

Model performance with hold-out test
data

</caption>

<thead>

<tr>

<th style="text-align:left;">

</th>

<th style="text-align:right;">

I

</th>

<th style="text-align:right;">

II

</th>

<th style="text-align:right;">

II/III

</th>

<th style="text-align:right;">

III

</th>

<th style="text-align:right;">

IV

</th>

<th style="text-align:right;">

IX

</th>

<th style="text-align:right;">

V

</th>

<th style="text-align:right;">

VI

</th>

<th style="text-align:right;">

VII

</th>

<th style="text-align:right;">

VIII

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

I

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

II

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

II/III

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

III

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

IV

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

IX

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

V

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

VI

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

VII

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

</tr>

<tr>

<td style="text-align:left;">

VIII

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1

</td>

</tr>

</tbody>

</table>

``` r
pred.accuracy = round(mean(test_predictions1 == testTransformed1$Cl.) * 100, 2)
```

And we find a prediction accuracy of 80 % in this case, not bad.

In Appendix 3, Hernández Fernández (2001) shows the results of his LDA
model applied to the locations in his training data set. He appears to
have tested his model on the same data set that he used to train it,
which is not ideal. Perhaps not suprisingly, there is a very good fit,
and he claims a 98% correct classification rate. Only one location was
incorrect, the same as we observed with our training data. From these
results we can conclude that we have successfully reproduced some key
results of Hernández Fernández
(2001).

### Hernández Fernández (2003) and whole fauna biospectra at 13 new locations

In his 2003 paper, Hernández Fernández attempts to infer past climatic
conditions using mammal fossil associations as source data. In this 2003
paper we also have 13 new recent faunas presented for testing. So let’s
attempt to reproduce Hernández Fernández’s results with these 13 new
fauna samples. We will train a LDA model on all 50 locations from the
2001 paper. Then we will use this model to classify the climate zones of
these 13 new locations in the 2003 paper. Then we’ll check to see how
accurate the model was with these 13 new locations. This is a direct
reproduction of the key results in the 2003 paper.

Here are the 13 new locations from appendix 3.1 in the 2003 paper that
we will use as the test dataset, just as Hernández Fernández does:

``` r
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

<table>

<caption>

Whole mammal biospectra from 13 new locations

</caption>

<thead>

<tr>

<th style="text-align:left;">

Whole.mammal

</th>

<th style="text-align:left;">

I

</th>

<th style="text-align:left;">

II

</th>

<th style="text-align:left;">

II.III

</th>

<th style="text-align:left;">

III

</th>

<th style="text-align:left;">

IV

</th>

<th style="text-align:left;">

V

</th>

<th style="text-align:left;">

VI

</th>

<th style="text-align:left;">

VII

</th>

<th style="text-align:left;">

VIII

</th>

<th style="text-align:left;">

IX

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Ust Kamchatsk

</td>

<td style="text-align:left;">

0.571

</td>

<td style="text-align:left;">

1.016

</td>

<td style="text-align:left;">

0.444

</td>

<td style="text-align:left;">

1.016

</td>

<td style="text-align:left;">

4.721

</td>

<td style="text-align:left;">

1.587

</td>

<td style="text-align:left;">

8.387

</td>

<td style="text-align:left;">

4.387

</td>

<td style="text-align:left;">

52.387

</td>

<td style="text-align:left;">

25.483

</td>

</tr>

<tr>

<td style="text-align:left;">

Saskatoon

</td>

<td style="text-align:left;">

1.298

</td>

<td style="text-align:left;">

1.963

</td>

<td style="text-align:left;">

5.113

</td>

<td style="text-align:left;">

4.779

</td>

<td style="text-align:left;">

6.604

</td>

<td style="text-align:left;">

6.107

</td>

<td style="text-align:left;">

24.311

</td>

<td style="text-align:left;">

18.755

</td>

<td style="text-align:left;">

27.496

</td>

<td style="text-align:left;">

3.574

</td>

</tr>

<tr>

<td style="text-align:left;">

Riga

</td>

<td style="text-align:left;">

1.124

</td>

<td style="text-align:left;">

1.356

</td>

<td style="text-align:left;">

1.058

</td>

<td style="text-align:left;">

1.058

</td>

<td style="text-align:left;">

12.970

</td>

<td style="text-align:left;">

3.112

</td>

<td style="text-align:left;">

39.883

</td>

<td style="text-align:left;">

9.897

</td>

<td style="text-align:left;">

24.878

</td>

<td style="text-align:left;">

4.661

</td>

</tr>

<tr>

<td style="text-align:left;">

Budapest

</td>

<td style="text-align:left;">

0.818

</td>

<td style="text-align:left;">

1.203

</td>

<td style="text-align:left;">

1.289

</td>

<td style="text-align:left;">

1.794

</td>

<td style="text-align:left;">

20.560

</td>

<td style="text-align:left;">

3.237

</td>

<td style="text-align:left;">

42.656

</td>

<td style="text-align:left;">

16.556

</td>

<td style="text-align:left;">

10.517

</td>

<td style="text-align:left;">

1.370

</td>

</tr>

<tr>

<td style="text-align:left;">

Dairen

</td>

<td style="text-align:left;">

5.451

</td>

<td style="text-align:left;">

5.809

</td>

<td style="text-align:left;">

2.929

</td>

<td style="text-align:left;">

1.639

</td>

<td style="text-align:left;">

7.883

</td>

<td style="text-align:left;">

14.442

</td>

<td style="text-align:left;">

32.291

</td>

<td style="text-align:left;">

13.336

</td>

<td style="text-align:left;">

13.144

</td>

<td style="text-align:left;">

3.077

</td>

</tr>

<tr>

<td style="text-align:left;">

Portland

</td>

<td style="text-align:left;">

1.492

</td>

<td style="text-align:left;">

2.966

</td>

<td style="text-align:left;">

6.119

</td>

<td style="text-align:left;">

6.940

</td>

<td style="text-align:left;">

16.004

</td>

<td style="text-align:left;">

5.704

</td>

<td style="text-align:left;">

34.021

</td>

<td style="text-align:left;">

12.428

</td>

<td style="text-align:left;">

12.022

</td>

<td style="text-align:left;">

2.306

</td>

</tr>

<tr>

<td style="text-align:left;">

Charleston

</td>

<td style="text-align:left;">

4.803

</td>

<td style="text-align:left;">

7.690

</td>

<td style="text-align:left;">

5.254

</td>

<td style="text-align:left;">

4.539

</td>

<td style="text-align:left;">

5.138

</td>

<td style="text-align:left;">

39.152

</td>

<td style="text-align:left;">

19.113

</td>

<td style="text-align:left;">

10.005

</td>

<td style="text-align:left;">

3.975

</td>

<td style="text-align:left;">

0.332

</td>

</tr>

<tr>

<td style="text-align:left;">

Niza

</td>

<td style="text-align:left;">

1.600

</td>

<td style="text-align:left;">

2.298

</td>

<td style="text-align:left;">

1.873

</td>

<td style="text-align:left;">

2.386

</td>

<td style="text-align:left;">

30.236

</td>

<td style="text-align:left;">

4.364

</td>

<td style="text-align:left;">

37.236

</td>

<td style="text-align:left;">

10.221

</td>

<td style="text-align:left;">

8.705

</td>

<td style="text-align:left;">

1.083

</td>

</tr>

<tr>

<td style="text-align:left;">

Kweilin

</td>

<td style="text-align:left;">

20.065

</td>

<td style="text-align:left;">

23.732

</td>

<td style="text-align:left;">

6.279

</td>

<td style="text-align:left;">

1.127

</td>

<td style="text-align:left;">

2.598

</td>

<td style="text-align:left;">

31.465

</td>

<td style="text-align:left;">

11.132

</td>

<td style="text-align:left;">

2.194

</td>

<td style="text-align:left;">

1.408

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Smara

</td>

<td style="text-align:left;">

4.359

</td>

<td style="text-align:left;">

6.996

</td>

<td style="text-align:left;">

15.971

</td>

<td style="text-align:left;">

35.018

</td>

<td style="text-align:left;">

23.480

</td>

<td style="text-align:left;">

5.018

</td>

<td style="text-align:left;">

2.198

</td>

<td style="text-align:left;">

4.762

</td>

<td style="text-align:left;">

1.099

</td>

<td style="text-align:left;">

1.099

</td>

</tr>

<tr>

<td style="text-align:left;">

Puerto Ayacucho

</td>

<td style="text-align:left;">

31.994

</td>

<td style="text-align:left;">

33.612

</td>

<td style="text-align:left;">

26.749

</td>

<td style="text-align:left;">

1.112

</td>

<td style="text-align:left;">

0.566

</td>

<td style="text-align:left;">

4.886

</td>

<td style="text-align:left;">

0.426

</td>

<td style="text-align:left;">

0.573

</td>

<td style="text-align:left;">

0.082

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Tabou

</td>

<td style="text-align:left;">

63.442

</td>

<td style="text-align:left;">

20.721

</td>

<td style="text-align:left;">

5.811

</td>

<td style="text-align:left;">

3.453

</td>

<td style="text-align:left;">

2.671

</td>

<td style="text-align:left;">

3.805

</td>

<td style="text-align:left;">

0.097

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Kuala Lumpur

</td>

<td style="text-align:left;">

71.967

</td>

<td style="text-align:left;">

18.593

</td>

<td style="text-align:left;">

4.840

</td>

<td style="text-align:left;">

0.499

</td>

<td style="text-align:left;">

0.536

</td>

<td style="text-align:left;">

2.386

</td>

<td style="text-align:left;">

0.955

</td>

<td style="text-align:left;">

0.156

</td>

<td style="text-align:left;">

0.068

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

</tbody>

</table>

Here is the LDA model that we generate from all 50 locations in the 2001
paper. Hernández Fernández used SPSS, so we don’t know the exact
configuration of his model. But we can see elshwere online that R and
SPSS can give good agreement with LDA.

``` r
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

Table 2 in the 2003 paper shows the actual Climate zones for the 13 new
locations, so let’s import that here so we can compare with our model’s
predictions:

``` r
fh_2003_tbl_2 <- read_csv(here::here("data/Fernández-2003-table-2.csv"))
fh_2003_tbl_2_no_na <- filter(fh_2003_tbl_2, !is.na(`Climate zone`))

kable(fh_2003_tbl_2_no_na, caption = "Actual climate zones of the 13 new locations")
```

<table>

<caption>

Actual climate zones of the 13 new locations

</caption>

<thead>

<tr>

<th style="text-align:right;">

N

</th>

<th style="text-align:left;">

Climate zone

</th>

<th style="text-align:left;">

Locality

</th>

<th style="text-align:left;">

Country

</th>

<th style="text-align:left;">

Latitude

</th>

<th style="text-align:left;">

Longitude

</th>

<th style="text-align:left;">

Altitude

</th>

<th style="text-align:left;">

References

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:right;">

51

</td>

<td style="text-align:left;">

VIII/IX

</td>

<td style="text-align:left;">

Ust Kamchatsk

</td>

<td style="text-align:left;">

Russia

</td>

<td style="text-align:left;">

56°14′‐N

</td>

<td style="text-align:left;">

162°28′‐E

</td>

<td style="text-align:left;">

6 m

</td>

<td style="text-align:left;">

Corbet (1978)

</td>

</tr>

<tr>

<td style="text-align:right;">

52

</td>

<td style="text-align:left;">

VI/VIII

</td>

<td style="text-align:left;">

Saskatoon

</td>

<td style="text-align:left;">

Canada

</td>

<td style="text-align:left;">

52°08′‐N

</td>

<td style="text-align:left;">

106°38′‐W

</td>

<td style="text-align:left;">

157 m

</td>

<td style="text-align:left;">

Hall (1981)

</td>

</tr>

<tr>

<td style="text-align:right;">

53

</td>

<td style="text-align:left;">

VI/VIII

</td>

<td style="text-align:left;">

Riga

</td>

<td style="text-align:left;">

Latvia

</td>

<td style="text-align:left;">

56°58′‐N

</td>

<td style="text-align:left;">

24°04′‐E

</td>

<td style="text-align:left;">

3 m

</td>

<td style="text-align:left;">

Corbet (1978)

</td>

</tr>

<tr>

<td style="text-align:right;">

54

</td>

<td style="text-align:left;">

VI/VII

</td>

<td style="text-align:left;">

Budapest

</td>

<td style="text-align:left;">

Hungary

</td>

<td style="text-align:left;">

47°31′‐N

</td>

<td style="text-align:left;">

19°02′‐E

</td>

<td style="text-align:left;">

120 m

</td>

<td style="text-align:left;">

Corbet (1978)

</td>

</tr>

<tr>

<td style="text-align:right;">

55

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

Dairen

</td>

<td style="text-align:left;">

China

</td>

<td style="text-align:left;">

38°54′‐N

</td>

<td style="text-align:left;">

121°38′‐E

</td>

<td style="text-align:left;">

96 m

</td>

<td style="text-align:left;">

Corbet (1978)

</td>

</tr>

<tr>

<td style="text-align:right;">

56

</td>

<td style="text-align:left;">

IV/VI

</td>

<td style="text-align:left;">

Portland

</td>

<td style="text-align:left;">

USA

</td>

<td style="text-align:left;">

45°32′‐N

</td>

<td style="text-align:left;">

122°40′‐W

</td>

<td style="text-align:left;">

9 m

</td>

<td style="text-align:left;">

Hall (1981)

</td>

</tr>

<tr>

<td style="text-align:right;">

57

</td>

<td style="text-align:left;">

V

</td>

<td style="text-align:left;">

Charleston

</td>

<td style="text-align:left;">

USA

</td>

<td style="text-align:left;">

32°54′‐N

</td>

<td style="text-align:left;">

80°02′‐W

</td>

<td style="text-align:left;">

100 m

</td>

<td style="text-align:left;">

Hall (1981)

</td>

</tr>

<tr>

<td style="text-align:right;">

58

</td>

<td style="text-align:left;">

IV/VI

</td>

<td style="text-align:left;">

Niza

</td>

<td style="text-align:left;">

France

</td>

<td style="text-align:left;">

43°40′‐N

</td>

<td style="text-align:left;">

7°12′‐E

</td>

<td style="text-align:left;">

5 m

</td>

<td style="text-align:left;">

Corbet (1978)

</td>

</tr>

<tr>

<td style="text-align:right;">

59

</td>

<td style="text-align:left;">

II/V

</td>

<td style="text-align:left;">

Kweilin

</td>

<td style="text-align:left;">

China

</td>

<td style="text-align:left;">

25°15′‐N

</td>

<td style="text-align:left;">

110°10′‐E

</td>

<td style="text-align:left;">

167 m

</td>

<td style="text-align:left;">

Corbet (1978);

</td>

</tr>

<tr>

<td style="text-align:right;">

60

</td>

<td style="text-align:left;">

III

</td>

<td style="text-align:left;">

Smara

</td>

<td style="text-align:left;">

Sahara W

</td>

<td style="text-align:left;">

26°44′‐N

</td>

<td style="text-align:left;">

11°26′‐W

</td>

<td style="text-align:left;">

140 m

</td>

<td style="text-align:left;">

Corbet (1978)

</td>

</tr>

<tr>

<td style="text-align:right;">

61

</td>

<td style="text-align:left;">

II

</td>

<td style="text-align:left;">

Puerto Ayacucho

</td>

<td style="text-align:left;">

Venezuela

</td>

<td style="text-align:left;">

5°41′‐N

</td>

<td style="text-align:left;">

67°38′‐W

</td>

<td style="text-align:left;">

99 m

</td>

<td style="text-align:left;">

Eisenberg (1989)

</td>

</tr>

<tr>

<td style="text-align:right;">

62

</td>

<td style="text-align:left;">

I/II

</td>

<td style="text-align:left;">

Tabou

</td>

<td style="text-align:left;">

Ivory Coast

</td>

<td style="text-align:left;">

4°55′‐N

</td>

<td style="text-align:left;">

7°22′‐E

</td>

<td style="text-align:left;">

4 m

</td>

<td style="text-align:left;">

Kingdon (1971–82); Dorst & Dandelot (1973); Nowak (1991)

</td>

</tr>

<tr>

<td style="text-align:right;">

63

</td>

<td style="text-align:left;">

I

</td>

<td style="text-align:left;">

Kuala Lumpur

</td>

<td style="text-align:left;">

Malaysia

</td>

<td style="text-align:left;">

3°07′‐N

</td>

<td style="text-align:left;">

101°42′‐E

</td>

<td style="text-align:left;">

34 m

</td>

<td style="text-align:left;">

Corbet & Hill (1992)

</td>

</tr>

</tbody>

</table>

Now we can inspect the model on the testing data set of the 13 new
locations from the 2003 paper. Below we can see a pretty accurate
classification from our model’s predictions and the actual climate zone
reported in Hernández Fernández (2003) table
2:

``` r
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

<table>

<caption>

Model predictions versus actual climate zones of the 13 new locations

</caption>

<thead>

<tr>

<th style="text-align:left;">

Location

</th>

<th style="text-align:left;">

Predicted by our model

</th>

<th style="text-align:left;">

Actual zone from table 2

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Ust Kamchatsk

</td>

<td style="text-align:left;">

VIII

</td>

<td style="text-align:left;">

VIII/IX

</td>

</tr>

<tr>

<td style="text-align:left;">

Saskatoon

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

VI/VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Riga

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

VI/VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Budapest

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

VI/VII

</td>

</tr>

<tr>

<td style="text-align:left;">

Dairen

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Portland

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

IV/VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Charleston

</td>

<td style="text-align:left;">

V

</td>

<td style="text-align:left;">

V

</td>

</tr>

<tr>

<td style="text-align:left;">

Niza

</td>

<td style="text-align:left;">

IV

</td>

<td style="text-align:left;">

IV/VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Kweilin

</td>

<td style="text-align:left;">

V

</td>

<td style="text-align:left;">

II/V

</td>

</tr>

<tr>

<td style="text-align:left;">

Smara

</td>

<td style="text-align:left;">

III

</td>

<td style="text-align:left;">

III

</td>

</tr>

<tr>

<td style="text-align:left;">

Puerto Ayacucho

</td>

<td style="text-align:left;">

II/III

</td>

<td style="text-align:left;">

II

</td>

</tr>

<tr>

<td style="text-align:left;">

Tabou

</td>

<td style="text-align:left;">

I

</td>

<td style="text-align:left;">

I/II

</td>

</tr>

<tr>

<td style="text-align:left;">

Kuala Lumpur

</td>

<td style="text-align:left;">

I

</td>

<td style="text-align:left;">

I

</td>

</tr>

</tbody>

</table>

### Hernández Fernández (2003) and Rodentia data

Let’s take a look at how effective this model is with the Rodent data.
In his 2003 paper we see ‘Appendix 1.2 Rodentia faunal bioclimatic
spectra of the localities’ which appears to be model training data for
predicting climate zones from rodent data only. The rodent data of 13
new locations are given in ‘Appendix 3.2 Rodentia faunal bioclimatic
spectra of the 13 new localities used for the validation of the
bioclimatic model.’ So let’s see if we can reproduce the rodent
analysis. We’ll generate a new LDA model using the rodent data from 50
locations as the training set, then predict the locations of the 13 new
faunas using only their rodent data.

``` r
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

<table>

<caption>

Rodent data from 50 locations

</caption>

<thead>

<tr>

<th style="text-align:left;">

Locality

</th>

<th style="text-align:left;">

I

</th>

<th style="text-align:left;">

II

</th>

<th style="text-align:left;">

II.III

</th>

<th style="text-align:left;">

III

</th>

<th style="text-align:left;">

IV

</th>

<th style="text-align:left;">

V

</th>

<th style="text-align:left;">

VI

</th>

<th style="text-align:left;">

VII

</th>

<th style="text-align:left;">

VIII

</th>

<th style="text-align:left;">

IX

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Barrow

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

6.667

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

46.667

</td>

<td style="text-align:left;">

46.667

</td>

</tr>

<tr>

<td style="text-align:left;">

Port Harrison

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

22.222

</td>

<td style="text-align:left;">

22.222

</td>

<td style="text-align:left;">

22.222

</td>

<td style="text-align:left;">

33.333

</td>

</tr>

<tr>

<td style="text-align:left;">

Malye-Karmaerly

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

25.000

</td>

<td style="text-align:left;">

75.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Mys Chelyuskin

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

25.000

</td>

<td style="text-align:left;">

75.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Mys Schmidta

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

6.667

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

46.667

</td>

<td style="text-align:left;">

46.667

</td>

</tr>

<tr>

<td style="text-align:left;">

Fairbanks

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

1.333

</td>

<td style="text-align:left;">

1.333

</td>

<td style="text-align:left;">

1.333

</td>

<td style="text-align:left;">

3.000

</td>

<td style="text-align:left;">

24.333

</td>

<td style="text-align:left;">

8.778

</td>

<td style="text-align:left;">

51.000

</td>

<td style="text-align:left;">

8.889

</td>

</tr>

<tr>

<td style="text-align:left;">

Fort Smith

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

2.619

</td>

<td style="text-align:left;">

2.619

</td>

<td style="text-align:left;">

2.619

</td>

<td style="text-align:left;">

3.214

</td>

<td style="text-align:left;">

23.690

</td>

<td style="text-align:left;">

16.548

</td>

<td style="text-align:left;">

48.690

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Kajaani

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

8.586

</td>

<td style="text-align:left;">

3.283

</td>

<td style="text-align:left;">

29.798

</td>

<td style="text-align:left;">

3.283

</td>

<td style="text-align:left;">

47.980

</td>

<td style="text-align:left;">

3.030

</td>

</tr>

<tr>

<td style="text-align:left;">

Serov

</td>

<td style="text-align:left;">

0.654

</td>

<td style="text-align:left;">

0.654

</td>

<td style="text-align:left;">

0.654

</td>

<td style="text-align:left;">

0.654

</td>

<td style="text-align:left;">

5.556

</td>

<td style="text-align:left;">

2.124

</td>

<td style="text-align:left;">

27.124

</td>

<td style="text-align:left;">

7.026

</td>

<td style="text-align:left;">

53.595

</td>

<td style="text-align:left;">

1.961

</td>

</tr>

<tr>

<td style="text-align:left;">

Erbogachen

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

8.681

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

19.097

</td>

<td style="text-align:left;">

4.514

</td>

<td style="text-align:left;">

56.597

</td>

<td style="text-align:left;">

4.167

</td>

</tr>

<tr>

<td style="text-align:left;">

Rapid City

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.909

</td>

<td style="text-align:left;">

6.136

</td>

<td style="text-align:left;">

5.227

</td>

<td style="text-align:left;">

3.712

</td>

<td style="text-align:left;">

4.470

</td>

<td style="text-align:left;">

22.955

</td>

<td style="text-align:left;">

44.924

</td>

<td style="text-align:left;">

11.667

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Santa Cruz

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

13.542

</td>

<td style="text-align:left;">

7.292

</td>

<td style="text-align:left;">

7.292

</td>

<td style="text-align:left;">

4.167

</td>

<td style="text-align:left;">

16.667

</td>

<td style="text-align:left;">

51.042

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Fort Schevchenko

</td>

<td style="text-align:left;">

0.505

</td>

<td style="text-align:left;">

0.505

</td>

<td style="text-align:left;">

0.505

</td>

<td style="text-align:left;">

2.020

</td>

<td style="text-align:left;">

10.354

</td>

<td style="text-align:left;">

0.505

</td>

<td style="text-align:left;">

4.293

</td>

<td style="text-align:left;">

80.808

</td>

<td style="text-align:left;">

0.505

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Urumchi

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

5.556

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

87.374

</td>

<td style="text-align:left;">

1.010

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Paotou

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

95.767

</td>

<td style="text-align:left;">

0.529

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Prince Rupert

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

2.821

</td>

<td style="text-align:left;">

2.821

</td>

<td style="text-align:left;">

4.744

</td>

<td style="text-align:left;">

3.462

</td>

<td style="text-align:left;">

30.000

</td>

<td style="text-align:left;">

18.462

</td>

<td style="text-align:left;">

37.692

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Cleveland

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

3.137

</td>

<td style="text-align:left;">

3.333

</td>

<td style="text-align:left;">

2.157

</td>

<td style="text-align:left;">

2.157

</td>

<td style="text-align:left;">

13.627

</td>

<td style="text-align:left;">

42.255

</td>

<td style="text-align:left;">

13.824

</td>

<td style="text-align:left;">

19.510

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Evangelistas

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

1.818

</td>

<td style="text-align:left;">

11.667

</td>

<td style="text-align:left;">

5.303

</td>

<td style="text-align:left;">

8.333

</td>

<td style="text-align:left;">

7.879

</td>

<td style="text-align:left;">

44.242

</td>

<td style="text-align:left;">

20.758

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Vlissingen

</td>

<td style="text-align:left;">

2.116

</td>

<td style="text-align:left;">

2.116

</td>

<td style="text-align:left;">

2.116

</td>

<td style="text-align:left;">

2.116

</td>

<td style="text-align:left;">

13.228

</td>

<td style="text-align:left;">

4.200

</td>

<td style="text-align:left;">

45.172

</td>

<td style="text-align:left;">

3.009

</td>

<td style="text-align:left;">

23.148

</td>

<td style="text-align:left;">

2.778

</td>

</tr>

<tr>

<td style="text-align:left;">

Tsingtao

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

5.556

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

4.514

</td>

<td style="text-align:left;">

8.681

</td>

<td style="text-align:left;">

46.181

</td>

<td style="text-align:left;">

26.389

</td>

<td style="text-align:left;">

4.514

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

New Orleans

</td>

<td style="text-align:left;">

1.538

</td>

<td style="text-align:left;">

6.667

</td>

<td style="text-align:left;">

4.103

</td>

<td style="text-align:left;">

4.103

</td>

<td style="text-align:left;">

1.538

</td>

<td style="text-align:left;">

52.436

</td>

<td style="text-align:left;">

20.128

</td>

<td style="text-align:left;">

6.026

</td>

<td style="text-align:left;">

3.462

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Puerto Montt

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

2.500

</td>

<td style="text-align:left;">

5.625

</td>

<td style="text-align:left;">

4.167

</td>

<td style="text-align:left;">

15.625

</td>

<td style="text-align:left;">

55.625

</td>

<td style="text-align:left;">

13.958

</td>

<td style="text-align:left;">

2.500

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Corrientes

</td>

<td style="text-align:left;">

7.738

</td>

<td style="text-align:left;">

42.500

</td>

<td style="text-align:left;">

15.119

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

31.786

</td>

<td style="text-align:left;">

1.429

</td>

<td style="text-align:left;">

1.429

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Foochow

</td>

<td style="text-align:left;">

11.788

</td>

<td style="text-align:left;">

28.455

</td>

<td style="text-align:left;">

2.965

</td>

<td style="text-align:left;">

2.965

</td>

<td style="text-align:left;">

2.965

</td>

<td style="text-align:left;">

37.278

</td>

<td style="text-align:left;">

10.808

</td>

<td style="text-align:left;">

0.654

</td>

<td style="text-align:left;">

2.124

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Kagoshima

</td>

<td style="text-align:left;">

4.894

</td>

<td style="text-align:left;">

2.116

</td>

<td style="text-align:left;">

2.116

</td>

<td style="text-align:left;">

2.116

</td>

<td style="text-align:left;">

4.200

</td>

<td style="text-align:left;">

40.311

</td>

<td style="text-align:left;">

36.144

</td>

<td style="text-align:left;">

0.926

</td>

<td style="text-align:left;">

7.176

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Fresno

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

4.444

</td>

<td style="text-align:left;">

8.222

</td>

<td style="text-align:left;">

8.222

</td>

<td style="text-align:left;">

47.333

</td>

<td style="text-align:left;">

1.333

</td>

<td style="text-align:left;">

14.000

</td>

<td style="text-align:left;">

11.778

</td>

<td style="text-align:left;">

4.667

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Santiago de Chile

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

6.944

</td>

<td style="text-align:left;">

7.639

</td>

<td style="text-align:left;">

61.111

</td>

<td style="text-align:left;">

10.417

</td>

<td style="text-align:left;">

11.806

</td>

<td style="text-align:left;">

2.083

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Sanlucar de Barrameda

</td>

<td style="text-align:left;">

2.822

</td>

<td style="text-align:left;">

2.822

</td>

<td style="text-align:left;">

2.822

</td>

<td style="text-align:left;">

2.822

</td>

<td style="text-align:left;">

48.192

</td>

<td style="text-align:left;">

5.600

</td>

<td style="text-align:left;">

25.970

</td>

<td style="text-align:left;">

1.235

</td>

<td style="text-align:left;">

7.716

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Aleppo

</td>

<td style="text-align:left;">

1.270

</td>

<td style="text-align:left;">

2.270

</td>

<td style="text-align:left;">

2.270

</td>

<td style="text-align:left;">

7.270

</td>

<td style="text-align:left;">

55.603

</td>

<td style="text-align:left;">

2.520

</td>

<td style="text-align:left;">

17.103

</td>

<td style="text-align:left;">

8.639

</td>

<td style="text-align:left;">

3.056

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Esfahan

</td>

<td style="text-align:left;">

0.585

</td>

<td style="text-align:left;">

1.637

</td>

<td style="text-align:left;">

4.708

</td>

<td style="text-align:left;">

16.111

</td>

<td style="text-align:left;">

50.760

</td>

<td style="text-align:left;">

0.585

</td>

<td style="text-align:left;">

8.918

</td>

<td style="text-align:left;">

14.795

</td>

<td style="text-align:left;">

1.901

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Phoenix

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

4.808

</td>

<td style="text-align:left;">

21.859

</td>

<td style="text-align:left;">

41.731

</td>

<td style="text-align:left;">

4.231

</td>

<td style="text-align:left;">

1.731

</td>

<td style="text-align:left;">

6.603

</td>

<td style="text-align:left;">

15.897

</td>

<td style="text-align:left;">

3.141

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Arica

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

25.000

</td>

<td style="text-align:left;">

36.111

</td>

<td style="text-align:left;">

19.444

</td>

<td style="text-align:left;">

11.111

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

8.333

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Asswan

</td>

<td style="text-align:left;">

3.243

</td>

<td style="text-align:left;">

6.814

</td>

<td style="text-align:left;">

13.957

</td>

<td style="text-align:left;">

37.766

</td>

<td style="text-align:left;">

25.266

</td>

<td style="text-align:left;">

3.600

</td>

<td style="text-align:left;">

3.600

</td>

<td style="text-align:left;">

3.175

</td>

<td style="text-align:left;">

2.579

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Mascate

</td>

<td style="text-align:left;">

5.079

</td>

<td style="text-align:left;">

5.079

</td>

<td style="text-align:left;">

11.746

</td>

<td style="text-align:left;">

31.746

</td>

<td style="text-align:left;">

31.746

</td>

<td style="text-align:left;">

5.079

</td>

<td style="text-align:left;">

5.079

</td>

<td style="text-align:left;">

2.222

</td>

<td style="text-align:left;">

2.222

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Lüderitz Bay

</td>

<td style="text-align:left;">

1.754

</td>

<td style="text-align:left;">

8.246

</td>

<td style="text-align:left;">

17.018

</td>

<td style="text-align:left;">

56.491

</td>

<td style="text-align:left;">

12.632

</td>

<td style="text-align:left;">

3.860

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Las Piedras

</td>

<td style="text-align:left;">

26.667

</td>

<td style="text-align:left;">

26.667

</td>

<td style="text-align:left;">

46.667

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Catamarca

</td>

<td style="text-align:left;">

1.471

</td>

<td style="text-align:left;">

16.373

</td>

<td style="text-align:left;">

62.451

</td>

<td style="text-align:left;">

4.902

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

7.549

</td>

<td style="text-align:left;">

1.176

</td>

<td style="text-align:left;">

6.078

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Zinder

</td>

<td style="text-align:left;">

3.684

</td>

<td style="text-align:left;">

29.123

</td>

<td style="text-align:left;">

54.561

</td>

<td style="text-align:left;">

6.316

</td>

<td style="text-align:left;">

5.439

</td>

<td style="text-align:left;">

0.877

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Voi

</td>

<td style="text-align:left;">

10.889

</td>

<td style="text-align:left;">

44.074

</td>

<td style="text-align:left;">

24.074

</td>

<td style="text-align:left;">

8.630

</td>

<td style="text-align:left;">

5.556

</td>

<td style="text-align:left;">

6.778

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Jaipur

</td>

<td style="text-align:left;">

4.841

</td>

<td style="text-align:left;">

25.619

</td>

<td style="text-align:left;">

36.175

</td>

<td style="text-align:left;">

20.619

</td>

<td style="text-align:left;">

7.841

</td>

<td style="text-align:left;">

0.952

</td>

<td style="text-align:left;">

0.952

</td>

<td style="text-align:left;">

3.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Roque Saenz Peña

</td>

<td style="text-align:left;">

2.500

</td>

<td style="text-align:left;">

49.500

</td>

<td style="text-align:left;">

24.500

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

19.500

</td>

<td style="text-align:left;">

2.000

</td>

<td style="text-align:left;">

2.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Ziguinchor

</td>

<td style="text-align:left;">

21.923

</td>

<td style="text-align:left;">

48.846

</td>

<td style="text-align:left;">

22.564

</td>

<td style="text-align:left;">

2.051

</td>

<td style="text-align:left;">

2.051

</td>

<td style="text-align:left;">

2.564

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Moundou

</td>

<td style="text-align:left;">

18.986

</td>

<td style="text-align:left;">

43.261

</td>

<td style="text-align:left;">

23.696

</td>

<td style="text-align:left;">

2.319

</td>

<td style="text-align:left;">

4.855

</td>

<td style="text-align:left;">

6.884

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Mtwara

</td>

<td style="text-align:left;">

16.222

</td>

<td style="text-align:left;">

52.833

</td>

<td style="text-align:left;">

12.833

</td>

<td style="text-align:left;">

3.556

</td>

<td style="text-align:left;">

5.056

</td>

<td style="text-align:left;">

9.500

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Patna

</td>

<td style="text-align:left;">

6.052

</td>

<td style="text-align:left;">

40.357

</td>

<td style="text-align:left;">

32.024

</td>

<td style="text-align:left;">

12.579

</td>

<td style="text-align:left;">

4.940

</td>

<td style="text-align:left;">

1.190

</td>

<td style="text-align:left;">

1.190

</td>

<td style="text-align:left;">

1.667

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Tumaco

</td>

<td style="text-align:left;">

74.306

</td>

<td style="text-align:left;">

15.972

</td>

<td style="text-align:left;">

7.639

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

2.083

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Paramaribo

</td>

<td style="text-align:left;">

68.860

</td>

<td style="text-align:left;">

21.491

</td>

<td style="text-align:left;">

8.333

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

1.316

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Kribi

</td>

<td style="text-align:left;">

80.833

</td>

<td style="text-align:left;">

13.526

</td>

<td style="text-align:left;">

1.667

</td>

<td style="text-align:left;">

1.026

</td>

<td style="text-align:left;">

1.026

</td>

<td style="text-align:left;">

1.923

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Yangambi

</td>

<td style="text-align:left;">

68.295

</td>

<td style="text-align:left;">

19.922

</td>

<td style="text-align:left;">

3.643

</td>

<td style="text-align:left;">

1.628

</td>

<td style="text-align:left;">

2.093

</td>

<td style="text-align:left;">

4.419

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Medan

</td>

<td style="text-align:left;">

84.620

</td>

<td style="text-align:left;">

11.647

</td>

<td style="text-align:left;">

0.386

</td>

<td style="text-align:left;">

0.386

</td>

<td style="text-align:left;">

0.386

</td>

<td style="text-align:left;">

2.188

</td>

<td style="text-align:left;">

0.386

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

</tbody>

</table>

``` r
kable(hf_2003_appdx_3.2, caption="Rodent data from 13 locations")
```

<table>

<caption>

Rodent data from 13 locations

</caption>

<thead>

<tr>

<th style="text-align:left;">

Rodentia

</th>

<th style="text-align:left;">

I

</th>

<th style="text-align:left;">

II

</th>

<th style="text-align:left;">

II.III

</th>

<th style="text-align:left;">

III

</th>

<th style="text-align:left;">

IV

</th>

<th style="text-align:left;">

V

</th>

<th style="text-align:left;">

VI

</th>

<th style="text-align:left;">

VII

</th>

<th style="text-align:left;">

VIII

</th>

<th style="text-align:left;">

IX

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Ust Kamchatsk

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

4.167

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

8.333

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

52.083

</td>

<td style="text-align:left;">

35.417

</td>

</tr>

<tr>

<td style="text-align:left;">

Saskatoon

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

2.037

</td>

<td style="text-align:left;">

2.037

</td>

<td style="text-align:left;">

2.037

</td>

<td style="text-align:left;">

5.278

</td>

<td style="text-align:left;">

29.537

</td>

<td style="text-align:left;">

21.204

</td>

<td style="text-align:left;">

37.870

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Riga

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

9.337

</td>

<td style="text-align:left;">

2.965

</td>

<td style="text-align:left;">

46.592

</td>

<td style="text-align:left;">

2.124

</td>

<td style="text-align:left;">

31.046

</td>

<td style="text-align:left;">

1.961

</td>

</tr>

<tr>

<td style="text-align:left;">

Budapest

</td>

<td style="text-align:left;">

1.270

</td>

<td style="text-align:left;">

1.270

</td>

<td style="text-align:left;">

1.270

</td>

<td style="text-align:left;">

1.270

</td>

<td style="text-align:left;">

13.770

</td>

<td style="text-align:left;">

2.520

</td>

<td style="text-align:left;">

52.937

</td>

<td style="text-align:left;">

11.806

</td>

<td style="text-align:left;">

13.889

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Dairen

</td>

<td style="text-align:left;">

1.235

</td>

<td style="text-align:left;">

1.235

</td>

<td style="text-align:left;">

1.235

</td>

<td style="text-align:left;">

1.235

</td>

<td style="text-align:left;">

7.716

</td>

<td style="text-align:left;">

4.012

</td>

<td style="text-align:left;">

46.605

</td>

<td style="text-align:left;">

12.346

</td>

<td style="text-align:left;">

24.383

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Portland

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

2.444

</td>

<td style="text-align:left;">

2.444

</td>

<td style="text-align:left;">

20.778

</td>

<td style="text-align:left;">

3.000

</td>

<td style="text-align:left;">

49.333

</td>

<td style="text-align:left;">

9.333

</td>

<td style="text-align:left;">

12.667

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Charleston

</td>

<td style="text-align:left;">

1.667

</td>

<td style="text-align:left;">

4.444

</td>

<td style="text-align:left;">

1.667

</td>

<td style="text-align:left;">

1.667

</td>

<td style="text-align:left;">

1.667

</td>

<td style="text-align:left;">

54.722

</td>

<td style="text-align:left;">

22.500

</td>

<td style="text-align:left;">

7.222

</td>

<td style="text-align:left;">

4.444

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Niza

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

1.494

</td>

<td style="text-align:left;">

31.396

</td>

<td style="text-align:left;">

2.965

</td>

<td style="text-align:left;">

43.161

</td>

<td style="text-align:left;">

0.654

</td>

<td style="text-align:left;">

15.850

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Kweilin

</td>

<td style="text-align:left;">

17.758

</td>

<td style="text-align:left;">

21.925

</td>

<td style="text-align:left;">

3.175

</td>

<td style="text-align:left;">

3.175

</td>

<td style="text-align:left;">

3.175

</td>

<td style="text-align:left;">

28.175

</td>

<td style="text-align:left;">

19.841

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

1.389

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Smara

</td>

<td style="text-align:left;">

2.857

</td>

<td style="text-align:left;">

2.857

</td>

<td style="text-align:left;">

12.381

</td>

<td style="text-align:left;">

45.714

</td>

<td style="text-align:left;">

31.429

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

4.762

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Puerto Ayacucho

</td>

<td style="text-align:left;">

32.778

</td>

<td style="text-align:left;">

38.333

</td>

<td style="text-align:left;">

21.667

</td>

<td style="text-align:left;">

2.222

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

5.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Tabou

</td>

<td style="text-align:left;">

77.167

</td>

<td style="text-align:left;">

17.167

</td>

<td style="text-align:left;">

2.583

</td>

<td style="text-align:left;">

0.917

</td>

<td style="text-align:left;">

0.917

</td>

<td style="text-align:left;">

1.250

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

Kuala Lumpur

</td>

<td style="text-align:left;">

84.839

</td>

<td style="text-align:left;">

10.480

</td>

<td style="text-align:left;">

0.651

</td>

<td style="text-align:left;">

0.651

</td>

<td style="text-align:left;">

0.651

</td>

<td style="text-align:left;">

1.506

</td>

<td style="text-align:left;">

0.651

</td>

<td style="text-align:left;">

0.285

</td>

<td style="text-align:left;">

0.285

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

</tbody>

</table>

Here we generate a LDA model based on the rodent data from 50 locations,
as we see in Appendix 1.2 of the 2003 paper:

``` r
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

Now we use this rodent LDA model to classify the 13 new locations
presented in Appendix 3.2 of the 2003 paper. Once again we see a good
classification, all the predictions match up with the actual
zones.

``` r
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

<table>

<caption>

Model predictions versus actual climate zones for Rodent data from the
13 new locations

</caption>

<thead>

<tr>

<th style="text-align:left;">

Location

</th>

<th style="text-align:left;">

Predicted by our model

</th>

<th style="text-align:left;">

Actual zone from table 2

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Ust Kamchatsk

</td>

<td style="text-align:left;">

IX

</td>

<td style="text-align:left;">

VIII/IX

</td>

</tr>

<tr>

<td style="text-align:left;">

Saskatoon

</td>

<td style="text-align:left;">

VIII

</td>

<td style="text-align:left;">

VI/VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Riga

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

VI/VIII

</td>

</tr>

<tr>

<td style="text-align:left;">

Budapest

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

VI/VII

</td>

</tr>

<tr>

<td style="text-align:left;">

Dairen

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Portland

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

IV/VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Charleston

</td>

<td style="text-align:left;">

V

</td>

<td style="text-align:left;">

V

</td>

</tr>

<tr>

<td style="text-align:left;">

Niza

</td>

<td style="text-align:left;">

VI

</td>

<td style="text-align:left;">

IV/VI

</td>

</tr>

<tr>

<td style="text-align:left;">

Kweilin

</td>

<td style="text-align:left;">

V

</td>

<td style="text-align:left;">

II/V

</td>

</tr>

<tr>

<td style="text-align:left;">

Smara

</td>

<td style="text-align:left;">

III

</td>

<td style="text-align:left;">

III

</td>

</tr>

<tr>

<td style="text-align:left;">

Puerto Ayacucho

</td>

<td style="text-align:left;">

II

</td>

<td style="text-align:left;">

II

</td>

</tr>

<tr>

<td style="text-align:left;">

Tabou

</td>

<td style="text-align:left;">

I

</td>

<td style="text-align:left;">

I/II

</td>

</tr>

<tr>

<td style="text-align:left;">

Kuala Lumpur

</td>

<td style="text-align:left;">

I

</td>

<td style="text-align:left;">

I

</td>

</tr>

</tbody>

</table>

The coefficients of Hernández Fernández (2003) rodent-only model are
presented in ‘Appendix 2.2 Coefficients for discriminant functions
calculated from rodent faunal bioclimatic components and centroids, for
each discriminant function, for every locality’s biome group.’ We can
also show the coefficients from our rodent model.

Here are Hernández Fernández (2003) rodent LDA
coefficients:

``` r
kable(hf_2003_appdx_2.2, caption = "Hernández Fernández (2003) rodent-only coefficients")
```

<table>

<caption>

Hernández Fernández (2003) rodent-only coefficients

</caption>

<thead>

<tr>

<th style="text-align:left;">

Coefficients

</th>

<th style="text-align:left;">

Func..1

</th>

<th style="text-align:left;">

Func..2

</th>

<th style="text-align:left;">

Func..3

</th>

<th style="text-align:left;">

Func..4

</th>

<th style="text-align:left;">

Func..5

</th>

<th style="text-align:left;">

Func..6

</th>

<th style="text-align:left;">

Func..7

</th>

<th style="text-align:left;">

Func..8

</th>

<th style="text-align:left;">

Func..9

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

BC I

</td>

<td style="text-align:left;">

0.270

</td>

<td style="text-align:left;">

\-0.003

</td>

<td style="text-align:left;">

\-0.085

</td>

<td style="text-align:left;">

0.082

</td>

<td style="text-align:left;">

0.019

</td>

<td style="text-align:left;">

0.000

</td>

<td style="text-align:left;">

0.091

</td>

<td style="text-align:left;">

0.041

</td>

<td style="text-align:left;">

\-0.011

</td>

</tr>

<tr>

<td style="text-align:left;">

BC II

</td>

<td style="text-align:left;">

0.109

</td>

<td style="text-align:left;">

0.092

</td>

<td style="text-align:left;">

0.105

</td>

<td style="text-align:left;">

0.047

</td>

<td style="text-align:left;">

0.073

</td>

<td style="text-align:left;">

\-0.017

</td>

<td style="text-align:left;">

0.080

</td>

<td style="text-align:left;">

0.031

</td>

<td style="text-align:left;">

0.092

</td>

</tr>

<tr>

<td style="text-align:left;">

BC II/III

</td>

<td style="text-align:left;">

0.150

</td>

<td style="text-align:left;">

0.112

</td>

<td style="text-align:left;">

0.035

</td>

<td style="text-align:left;">

0.004

</td>

<td style="text-align:left;">

0.091

</td>

<td style="text-align:left;">

0.016

</td>

<td style="text-align:left;">

0.096

</td>

<td style="text-align:left;">

0.051

</td>

<td style="text-align:left;">

\-0.092

</td>

</tr>

<tr>

<td style="text-align:left;">

BC III

</td>

<td style="text-align:left;">

0.166

</td>

<td style="text-align:left;">

0.142

</td>

<td style="text-align:left;">

\-0.028

</td>

<td style="text-align:left;">

0.017

</td>

<td style="text-align:left;">

\-0.095

</td>

<td style="text-align:left;">

0.116

</td>

<td style="text-align:left;">

0.133

</td>

<td style="text-align:left;">

0.014

</td>

<td style="text-align:left;">

0.046

</td>

</tr>

<tr>

<td style="text-align:left;">

BC IV

</td>

<td style="text-align:left;">

0.064

</td>

<td style="text-align:left;">

0.214

</td>

<td style="text-align:left;">

\-0.053

</td>

<td style="text-align:left;">

0.085

</td>

<td style="text-align:left;">

0.030

</td>

<td style="text-align:left;">

\-0.088

</td>

<td style="text-align:left;">

0.046

</td>

<td style="text-align:left;">

0.074

</td>

<td style="text-align:left;">

0.000

</td>

</tr>

<tr>

<td style="text-align:left;">

BC V

</td>

<td style="text-align:left;">

0.230

</td>

<td style="text-align:left;">

0.025

</td>

<td style="text-align:left;">

0.145

</td>

<td style="text-align:left;">

0.137

</td>

<td style="text-align:left;">

\-0.079

</td>

<td style="text-align:left;">

0.008

</td>

<td style="text-align:left;">

0.049

</td>

<td style="text-align:left;">

0.091

</td>

<td style="text-align:left;">

\-0.032

</td>

</tr>

<tr>

<td style="text-align:left;">

BC VI

</td>

<td style="text-align:left;">

0.020

</td>

<td style="text-align:left;">

0.055

</td>

<td style="text-align:left;">

0.024

</td>

<td style="text-align:left;">

0.099

</td>

<td style="text-align:left;">

0.053

</td>

<td style="text-align:left;">

\-0.014

</td>

<td style="text-align:left;">

0.124

</td>

<td style="text-align:left;">

\-0.078

</td>

<td style="text-align:left;">

0.005

</td>

</tr>

<tr>

<td style="text-align:left;">

BC VII

</td>

<td style="text-align:left;">

0.051

</td>

<td style="text-align:left;">

0.071

</td>

<td style="text-align:left;">

\-0.005

</td>

<td style="text-align:left;">

0.113

</td>

<td style="text-align:left;">

0.060

</td>

<td style="text-align:left;">

0.064

</td>

<td style="text-align:left;">

0.057

</td>

<td style="text-align:left;">

0.060

</td>

<td style="text-align:left;">

0.007

</td>

</tr>

<tr>

<td style="text-align:left;">

BC VIII

</td>

<td style="text-align:left;">

0.034

</td>

<td style="text-align:left;">

0.030

</td>

<td style="text-align:left;">

0.013

</td>

<td style="text-align:left;">

0.053

</td>

<td style="text-align:left;">

0.015

</td>

<td style="text-align:left;">

\-0.004

</td>

<td style="text-align:left;">

0.158

</td>

<td style="text-align:left;">

0.128

</td>

<td style="text-align:left;">

0.004

</td>

</tr>

<tr>

<td style="text-align:left;">

Constante

</td>

<td style="text-align:left;">

\-10.749

</td>

<td style="text-align:left;">

\-7.541

</td>

<td style="text-align:left;">

\-1.485

</td>

<td style="text-align:left;">

\-6.660

</td>

<td style="text-align:left;">

\-2.593

</td>

<td style="text-align:left;">

\-0.543

</td>

<td style="text-align:left;">

\-8.725

</td>

<td style="text-align:left;">

\-4.180

</td>

<td style="text-align:left;">

0-.240

</td>

</tr>

</tbody>

</table>

And here are our rodent LDA coefficients, they are very different, and
it’s not clear why. I can see comparisons of SPSS and R LDA methods
online and they can get the same coefficients to within 2 decimal
places.

``` r
kable(ldaFit3$finalModel$scaling, , caption = "Our rodent-only coefficients")
```

<table>

<caption>

Our rodent-only coefficients

</caption>

<thead>

<tr>

<th style="text-align:left;">

</th>

<th style="text-align:right;">

LD1

</th>

<th style="text-align:right;">

LD2

</th>

<th style="text-align:right;">

LD3

</th>

<th style="text-align:right;">

LD4

</th>

<th style="text-align:right;">

LD5

</th>

<th style="text-align:right;">

LD6

</th>

<th style="text-align:right;">

LD7

</th>

<th style="text-align:right;">

LD8

</th>

<th style="text-align:right;">

LD9

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

I

</td>

<td style="text-align:right;">

\-3.9456120

</td>

<td style="text-align:right;">

1.8571946

</td>

<td style="text-align:right;">

\-2.2839127

</td>

<td style="text-align:right;">

\-0.2043386

</td>

<td style="text-align:right;">

0.4382413

</td>

<td style="text-align:right;">

\-0.0972965

</td>

<td style="text-align:right;">

0.1883264

</td>

<td style="text-align:right;">

\-0.0204625

</td>

<td style="text-align:right;">

\-0.3609093

</td>

</tr>

<tr>

<td style="text-align:left;">

II

</td>

<td style="text-align:right;">

\-0.2797960

</td>

<td style="text-align:right;">

\-0.5126244

</td>

<td style="text-align:right;">

1.2611972

</td>

<td style="text-align:right;">

0.1723866

</td>

<td style="text-align:right;">

\-0.4390170

</td>

<td style="text-align:right;">

\-0.7446459

</td>

<td style="text-align:right;">

\-0.2413689

</td>

<td style="text-align:right;">

0.0081013

</td>

<td style="text-align:right;">

1.3871780

</td>

</tr>

<tr>

<td style="text-align:left;">

II.III

</td>

<td style="text-align:right;">

\-1.0241057

</td>

<td style="text-align:right;">

\-0.7294386

</td>

<td style="text-align:right;">

0.2318606

</td>

<td style="text-align:right;">

0.9442555

</td>

<td style="text-align:right;">

\-0.7321051

</td>

<td style="text-align:right;">

\-0.2775421

</td>

<td style="text-align:right;">

0.1962765

</td>

<td style="text-align:right;">

0.0178003

</td>

<td style="text-align:right;">

\-1.4095681

</td>

</tr>

<tr>

<td style="text-align:left;">

III

</td>

<td style="text-align:right;">

\-0.9695998

</td>

<td style="text-align:right;">

\-0.6980201

</td>

<td style="text-align:right;">

\-0.4395415

</td>

<td style="text-align:right;">

0.4824274

</td>

<td style="text-align:right;">

0.6068351

</td>

<td style="text-align:right;">

1.8324211

</td>

<td style="text-align:right;">

0.7506079

</td>

<td style="text-align:right;">

\-0.2322662

</td>

<td style="text-align:right;">

0.5570933

</td>

</tr>

<tr>

<td style="text-align:left;">

IV

</td>

<td style="text-align:right;">

0.5882098

</td>

<td style="text-align:right;">

\-2.2320757

</td>

<td style="text-align:right;">

\-1.4289889

</td>

<td style="text-align:right;">

\-0.2194505

</td>

<td style="text-align:right;">

0.8020506

</td>

<td style="text-align:right;">

\-1.0958608

</td>

<td style="text-align:right;">

\-0.7320807

</td>

<td style="text-align:right;">

0.5580960

</td>

<td style="text-align:right;">

\-0.1203793

</td>

</tr>

<tr>

<td style="text-align:left;">

V

</td>

<td style="text-align:right;">

\-1.8408240

</td>

<td style="text-align:right;">

0.5208403

</td>

<td style="text-align:right;">

1.5959474

</td>

<td style="text-align:right;">

\-1.0701329

</td>

<td style="text-align:right;">

1.1697192

</td>

<td style="text-align:right;">

0.6228781

</td>

<td style="text-align:right;">

\-0.4270331

</td>

<td style="text-align:right;">

0.6261641

</td>

<td style="text-align:right;">

\-0.5096572

</td>

</tr>

<tr>

<td style="text-align:left;">

VI

</td>

<td style="text-align:right;">

0.9474207

</td>

<td style="text-align:right;">

0.1252653

</td>

<td style="text-align:right;">

0.1378399

</td>

<td style="text-align:right;">

\-0.1836348

</td>

<td style="text-align:right;">

0.1051836

</td>

<td style="text-align:right;">

\-0.6047633

</td>

<td style="text-align:right;">

0.5664057

</td>

<td style="text-align:right;">

\-1.6890092

</td>

<td style="text-align:right;">

0.1075054

</td>

</tr>

<tr>

<td style="text-align:left;">

VII

</td>

<td style="text-align:right;">

1.2723898

</td>

<td style="text-align:right;">

0.3318725

</td>

<td style="text-align:right;">

\-0.6340474

</td>

<td style="text-align:right;">

\-1.1374079

</td>

<td style="text-align:right;">

\-1.1453407

</td>

<td style="text-align:right;">

0.7336087

</td>

<td style="text-align:right;">

\-0.5776102

</td>

<td style="text-align:right;">

0.2896453

</td>

<td style="text-align:right;">

\-0.0596415

</td>

</tr>

<tr>

<td style="text-align:left;">

VIII

</td>

<td style="text-align:right;">

1.1380177

</td>

<td style="text-align:right;">

0.7122026

</td>

<td style="text-align:right;">

0.0084488

</td>

<td style="text-align:right;">

0.1163727

</td>

<td style="text-align:right;">

0.2443096

</td>

<td style="text-align:right;">

\-0.1868536

</td>

<td style="text-align:right;">

1.3254601

</td>

<td style="text-align:right;">

1.6516796

</td>

<td style="text-align:right;">

\-0.0875825

</td>

</tr>

<tr>

<td style="text-align:left;">

IX

</td>

<td style="text-align:right;">

1.4513957

</td>

<td style="text-align:right;">

1.1542276

</td>

<td style="text-align:right;">

\-0.0763914

</td>

<td style="text-align:right;">

1.3956945

</td>

<td style="text-align:right;">

0.6375633

</td>

<td style="text-align:right;">

0.2992819

</td>

<td style="text-align:right;">

\-1.5162156

</td>

<td style="text-align:right;">

\-0.6563265

</td>

<td style="text-align:right;">

\-0.0502355

</td>

</tr>

</tbody>

</table>

Let’s try it again without any pre-processing of the data, still our
coefficients are not similar to those published by Hernández Fernández
(2003)

``` r
# now run DFA using Linear Discriminant analysis (LDA) on the normalized data 

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

<table>

<caption>

Our rodent-only coefficients, no transformation to model inputs

</caption>

<thead>

<tr>

<th style="text-align:left;">

</th>

<th style="text-align:right;">

LD1

</th>

<th style="text-align:right;">

LD2

</th>

<th style="text-align:right;">

LD3

</th>

<th style="text-align:right;">

LD4

</th>

<th style="text-align:right;">

LD5

</th>

<th style="text-align:right;">

LD6

</th>

<th style="text-align:right;">

LD7

</th>

<th style="text-align:right;">

LD8

</th>

<th style="text-align:right;">

LD9

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

I

</td>

<td style="text-align:right;">

\-0.1699448

</td>

<td style="text-align:right;">

0.0799928

</td>

<td style="text-align:right;">

\-0.0983723

</td>

<td style="text-align:right;">

\-0.0088012

</td>

<td style="text-align:right;">

0.0188759

</td>

<td style="text-align:right;">

\-0.0041907

</td>

<td style="text-align:right;">

0.0081116

</td>

<td style="text-align:right;">

\-0.0008814

</td>

<td style="text-align:right;">

\-0.0155450

</td>

</tr>

<tr>

<td style="text-align:left;">

II

</td>

<td style="text-align:right;">

\-0.0169915

</td>

<td style="text-align:right;">

\-0.0311308

</td>

<td style="text-align:right;">

0.0765903

</td>

<td style="text-align:right;">

0.0104687

</td>

<td style="text-align:right;">

\-0.0266607

</td>

<td style="text-align:right;">

\-0.0452210

</td>

<td style="text-align:right;">

\-0.0146579

</td>

<td style="text-align:right;">

0.0004920

</td>

<td style="text-align:right;">

0.0842409

</td>

</tr>

<tr>

<td style="text-align:left;">

II.III

</td>

<td style="text-align:right;">

\-0.0687881

</td>

<td style="text-align:right;">

\-0.0489957

</td>

<td style="text-align:right;">

0.0155738

</td>

<td style="text-align:right;">

0.0634247

</td>

<td style="text-align:right;">

\-0.0491748

</td>

<td style="text-align:right;">

\-0.0186422

</td>

<td style="text-align:right;">

0.0131837

</td>

<td style="text-align:right;">

0.0011956

</td>

<td style="text-align:right;">

\-0.0946793

</td>

</tr>

<tr>

<td style="text-align:left;">

III

</td>

<td style="text-align:right;">

\-0.0817828

</td>

<td style="text-align:right;">

\-0.0588759

</td>

<td style="text-align:right;">

\-0.0370740

</td>

<td style="text-align:right;">

0.0406913

</td>

<td style="text-align:right;">

0.0511847

</td>

<td style="text-align:right;">

0.1545591

</td>

<td style="text-align:right;">

0.0633115

</td>

<td style="text-align:right;">

\-0.0195909

</td>

<td style="text-align:right;">

0.0469891

</td>

</tr>

<tr>

<td style="text-align:left;">

IV

</td>

<td style="text-align:right;">

0.0366497

</td>

<td style="text-align:right;">

\-0.1390744

</td>

<td style="text-align:right;">

\-0.0890363

</td>

<td style="text-align:right;">

\-0.0136733

</td>

<td style="text-align:right;">

0.0499735

</td>

<td style="text-align:right;">

\-0.0682800

</td>

<td style="text-align:right;">

\-0.0456139

</td>

<td style="text-align:right;">

0.0347734

</td>

<td style="text-align:right;">

\-0.0075005

</td>

</tr>

<tr>

<td style="text-align:left;">

V

</td>

<td style="text-align:right;">

\-0.1386407

</td>

<td style="text-align:right;">

0.0392268

</td>

<td style="text-align:right;">

0.1201979

</td>

<td style="text-align:right;">

\-0.0805965

</td>

<td style="text-align:right;">

0.0880968

</td>

<td style="text-align:right;">

0.0469117

</td>

<td style="text-align:right;">

\-0.0321618

</td>

<td style="text-align:right;">

0.0471592

</td>

<td style="text-align:right;">

\-0.0383846

</td>

</tr>

<tr>

<td style="text-align:left;">

VI

</td>

<td style="text-align:right;">

0.0657054

</td>

<td style="text-align:right;">

0.0086874

</td>

<td style="text-align:right;">

0.0095595

</td>

<td style="text-align:right;">

\-0.0127354

</td>

<td style="text-align:right;">

0.0072947

</td>

<td style="text-align:right;">

\-0.0419414

</td>

<td style="text-align:right;">

0.0392813

</td>

<td style="text-align:right;">

\-0.1171359

</td>

<td style="text-align:right;">

0.0074557

</td>

</tr>

<tr>

<td style="text-align:left;">

VII

</td>

<td style="text-align:right;">

0.0574823

</td>

<td style="text-align:right;">

0.0149929

</td>

<td style="text-align:right;">

\-0.0286441

</td>

<td style="text-align:right;">

\-0.0513843

</td>

<td style="text-align:right;">

\-0.0517426

</td>

<td style="text-align:right;">

0.0331420

</td>

<td style="text-align:right;">

\-0.0260945

</td>

<td style="text-align:right;">

0.0130852

</td>

<td style="text-align:right;">

\-0.0026944

</td>

</tr>

<tr>

<td style="text-align:left;">

VIII

</td>

<td style="text-align:right;">

0.0620679

</td>

<td style="text-align:right;">

0.0388438

</td>

<td style="text-align:right;">

0.0004608

</td>

<td style="text-align:right;">

0.0063470

</td>

<td style="text-align:right;">

0.0133247

</td>

<td style="text-align:right;">

\-0.0101911

</td>

<td style="text-align:right;">

0.0722911

</td>

<td style="text-align:right;">

0.0900832

</td>

<td style="text-align:right;">

\-0.0047768

</td>

</tr>

<tr>

<td style="text-align:left;">

IX

</td>

<td style="text-align:right;">

0.0976815

</td>

<td style="text-align:right;">

0.0776816

</td>

<td style="text-align:right;">

\-0.0051413

</td>

<td style="text-align:right;">

0.0939327

</td>

<td style="text-align:right;">

0.0429091

</td>

<td style="text-align:right;">

0.0201422

</td>

<td style="text-align:right;">

\-0.1020440

</td>

<td style="text-align:right;">

\-0.0441719

</td>

<td style="text-align:right;">

\-0.0033809

</td>

</tr>

</tbody>

</table>

#### References

Fernández, M. H. (2001). Bioclimatic discriminant capacity of
terrestrial mammal faunas. Global Ecology and Biogeography, 10(2),
189-204. <https://doi.org/10.1046/j.1466-822x.2001.00218.x>

Fernández, M. H., & Peláez‐Campomanes, P. (2003). The bioclimatic model:
a method of palaeoclimatic qualitative inference based on mammal
associations. *Global Ecology and Biogeography*, 12(6), 507-517.
<https://doi.org/10.1046/j.1466-822X.2003.00057.x>

``` r
sessioninfo::session_info()
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
    ##  kableExtra   * 1.1.0      2019-03-16 [1]
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
    ##  viridisLite    0.3.0      2018-02-01 [1]
    ##  webshot        0.5.1      2018-09-28 [1]
    ##  withr          2.1.2      2018-03-15 [1]
    ##  xfun           0.8        2019-06-25 [1]
    ##  xml2           1.2.0.9000 2019-06-03 [1]
    ##  yaml           2.2.0      2018-07-25 [1]
    ##  source                             
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
    ##  CRAN (R 3.6.0)                     
    ##  CRAN (R 3.6.0)                     
    ##  CRAN (R 3.6.0)                     
    ##  Github (hadley/xml2@e051ffa)       
    ##  CRAN (R 3.6.0)                     
    ## 
    ## [1] /Library/Frameworks/R.framework/Versions/3.6/Resources/library
