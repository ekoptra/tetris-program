Capstone Project Tetris Program
================

Berita utama yang digunakan [Sentimen Apa yang Paling Keras Memukul
IHSG? Omicron atau The
Fed?](https://investor.id/market-and-corporate/279885/sentimen-apa-yang-paling-keras-memukul-ihsg-omicron-atau-the-fed)

Sumber data ada dua

1.  [Data
    Covid-19](https://github.com/owid/covid-19-data/blob/master/public/data/README.md)
2.  [Data IHSG
    Harian](https://finance.yahoo.com/quote/%5EJKSE/history?p=%5EJKSE)

Notebook ini digunakan hanya untuk menghasilkan visualisasi. Kemudian
visualisasi tersebut diedit kembali menggunakan
[Canva.com](https://www.canva.com/) untuk menghasilkan infografis yang
ada pada file `Infografis.pdf`

``` r
library(dplyr)
library(ggplot2)
```

Preprocessing
=============

Data yang dibutuhkan adalah data pada masa varian delta dan omicron
sehingga data akan dibagi menjadi dua. 1. Masa Varian Delta Juni 2021 -
November 2021 2. Desember 2021 - Sekarang (18 Maret 2022) Hal tersebut
didasarkan pada kapan kedua varian tersebut mulai masuk di Indonesia.
Varian delta sering disebut sebagai covid gelombang kedua dan varian
omicron sebagai covid gelombang ketika.

Fungsi Untuk ambil Data
-----------------------

Fungsi ini digunakan untuk read data covid dan ihsg sekaligus
menyatukannya menjadi satu data frame dalam rentang tanggal tertentu
`start_date` sampai `end_date`

``` r
get_data <- function(start_date, end_date){
   covid <- read.csv('data/covid-indonesia.csv')
   ihsg <- read.csv('data/ihsg.csv')
   
   ihsg <- ihsg %>% 
      filter(Date >= start_date & Date <= end_date) %>%
      select(Date, Close)
   names(ihsg) <- tolower(names(ihsg))
   
   
   covid <- covid %>%
      filter(date >= start_date & date <= end_date)
   
   
   inner_join(ihsg, covid, by="date")
}
```

Read Data
---------

``` r
delta <- get_data("2021-06-01", "2021-11-30")
omicron <- get_data("2021-12-01", "2022-03-18")
```

Visualisasi Data
================

Membuat fungsi untuk visualialisasi data ihsg dan covid dalam line chart

``` r
get_vis_ihsg <- function(data, title){
   data %>%
      ggplot(aes(x = as.Date(date), y = close)) +
      geom_line(color="#EC5D5D", size=1) +
      scale_x_date(date_breaks = "1 month",
                   date_labels = "%b %Y") +
      labs(x = "",
           y ="Close",
           title=title) +
      theme_minimal() +
      theme(plot.title = element_text(hjust = 0.5),
            axis.title.y = element_text(size=10, vjust = 3),
            rect = element_rect(fill = "transparent"))
}

get_vis_covid <- function(data, title){
   data %>%
      ggplot(aes(x = as.Date(date), y = new_cases)) +
      geom_line(color="#EC5D5D", size=1) +
      scale_x_date(date_breaks = "1 month",
                   date_labels = "%b %Y") +
      scale_y_continuous(breaks = seq(from=0, to=50000, by=10000),
                         labels = seq(from=0, to=50, by=10)) +
      labs(x = "",
           y ="Jumlah Kasus (Ribu)",
           title=title) +
      theme_minimal() +
      theme(plot.title = element_text(hjust = 0.5),
            axis.title.y = element_text(size=10, vjust = 3),
            rect = element_rect(fill = "transparent"))
}
```

Masa Varian Delta
-----------------

``` r
vis_delta_1 <- get_vis_ihsg(delta,
                            title = "IHSG Close Juni-November 2021\n(Masa Varian Delta)")
ggsave(vis_delta_1, filename = "visualisasi/delta_ihsg.png",  bg = "transparent")
```

    ## Saving 7 x 5 in image

``` r
vis_delta_1
```

![](Tetris_Capstone_files/figure-markdown_github/unnamed-chunk-5-1.png)

``` r
vis_delta_2 <- get_vis_covid(delta, 
                             title = "Pertambahan Kasus Covid-19 Juni-November 2021\n(Masa Varian Delta)")
ggsave(vis_delta_2, filename = "visualisasi/delta_covid.png",  bg = "transparent")
```

    ## Saving 7 x 5 in image

``` r
vis_delta_2
```

![](Tetris_Capstone_files/figure-markdown_github/unnamed-chunk-6-1.png)

``` r
corr_delta <- round(cor(delta$close, delta$new_cases, method = "spearman"), 3)
vis_delta_3 <- delta %>%
   ggplot(aes(x=close, y=new_cases)) +
   geom_point() +
   scale_y_continuous(breaks = seq(from=0, to=50000, by=10000),
                         labels = seq(from=0, to=50, by=10)) +
   labs(x="IHSG Close",
        y="Pertambahan Covid (Ribu)",
        title="Hubungan Antara Kasus Covid-19 dengan IHSG Close\npada Masa Varian Delta") +
   annotate("text", x = 6600, y=30000, 
            label = paste("Korelasi = ", corr_delta), 
            color="#EC5D5D", size=5) +
   theme_minimal() +
   theme(plot.title = element_text(hjust = 0.5))

ggsave(vis_delta_3, filename = "visualisasi/delta_corr.png",  bg = "transparent")
```

    ## Saving 7 x 5 in image

``` r
vis_delta_3
```

![](Tetris_Capstone_files/figure-markdown_github/unnamed-chunk-7-1.png)

Masa Varian Omicron
-------------------

``` r
vis_omicron_1 <- get_vis_ihsg(omicron,
                            title = "IHSG Close Desember 2021-Maret 2022\n(Masa Varian Omicron)")
ggsave(vis_omicron_1, filename = "visualisasi/omicron_ihsg.png",  bg = "transparent")
```

    ## Saving 7 x 5 in image

``` r
vis_omicron_1
```

![](Tetris_Capstone_files/figure-markdown_github/unnamed-chunk-8-1.png)

``` r
vis_omicron_2 <- get_vis_covid(omicron, 
                             title = "Pertambahan Kasus Covid-19 Desember 2021-Maret 2022\n(Masa Varian Omicron)")
ggsave(vis_omicron_2, filename = "visualisasi/omicron_covid.png",  bg = "transparent")
```

    ## Saving 7 x 5 in image

``` r
vis_omicron_2
```

![](Tetris_Capstone_files/figure-markdown_github/unnamed-chunk-9-1.png)

``` r
corr_omicron <- round(cor(omicron$close, omicron$new_cases, method = "spearman"), 3)
vis_omicron_3 <- omicron %>%
   ggplot(aes(x=close, y=new_cases)) +
   geom_point() +
   scale_y_continuous(breaks = seq(from=0, to=50000, by=10000),
                         labels = seq(from=0, to=50, by=10)) +
   labs(x="IHSG Close",
        y="Pertambahan Covid (Ribu)",
        title="Hubungan Antara Kasus Covid-19 dengan IHSG Close\npada Masa Varian Omicron") +
   annotate("text", x = 6600, y=50000, 
            label = paste("Korelasi = ", corr_omicron), 
            color="#EC5D5D", size=5) +
   theme_minimal() +
   theme(plot.title = element_text(hjust = 0.5))

ggsave(vis_omicron_3, filename = "visualisasi/omicron_corr.png",  bg = "transparent")
```

    ## Saving 7 x 5 in image

``` r
vis_omicron_3
```

![](Tetris_Capstone_files/figure-markdown_github/unnamed-chunk-10-1.png)
