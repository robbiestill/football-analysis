# football-analysis

This repository will contain my random collection of R and Python templates for football data analysis and visualisations. 

## Examples

```{r setup, include=FALSE}
library(tidyverse)
library(ggplot2)
library(ggrepel)
library(grid)
library(ggtext)
library(RCurl)

background = "#242424"
text_colour = "#FFFFFF"
filler = "#2f4f4f"
primary = "#136D99"
secondary = "#F9124E"
tertiary = "#F59F45"
title_font = "Helvetica"

theme_rs <- function(){
  theme_bw() %+replace% 
    theme(plot.background = element_rect(fill = background), 
          panel.background = element_rect(fill = background), 
          panel.grid.major = element_line(colour = filler),
          panel.grid.minor = element_line(colour = filler), 
          axis.line = element_line(colour = text_colour), 
          axis.text = element_text(colour = text_colour), 
          text = element_text(colour = text_colour, family = title_font), 
          plot.title = element_markdown(hjust = 0, size = 12, 
                                        padding = unit(c(0, 0, 10, 0), "pt"),), 
          plot.subtitle = element_text(vjust = 3, hjust=0), 
          plot.margin = margin(1, 0.5, 0.5, 0.5, "cm"))
}

data <- read_csv("https://raw.githubusercontent.com/robbiestill/football-analysis/master/R/ggplot/playmakers.csv") %>% 
  mutate(colour = case_when(PassCompletion<80&xAper90>0.5 ~ "primary",
                            PassCompletion>87 ~ "secondary",
                            TRUE ~ "filler"))

get_png <- function(filename) {
  grid::rasterGrob(png::readPNG(filename), interpolate = TRUE)
}

logo <- get_png(getURLContent("https://raw.githubusercontent.com/robbiestill/football-analysis/master/R/ggplot/hz.png"))

```


```{r,echo=TRUE, message=FALSE}

ggplot(data, aes(x= PassCompletion, y= xAper90, label=player, 
                       col = colour))+
  geom_text_repel(
    aes(label=ifelse(xAper90>0.5|PassCompletion>87|player == "Bruno Fernandes",
                     as.character(player),'')),
    box.padding = 1, 
    col = text_colour, 
    alpha = 0.5
  ) +
  geom_point() +
  scale_color_manual(values = c("primary" = primary, "secondary" =
                                  secondary, "filler" = filler)) +
  theme_rs() + 
  theme(legend.position = "none") + 
  labs(title = paste0("<span style='font-family:",title_font,
    "'>Elite playmakers: <span style='color:", 
    primary,";'>high</span> or <span style='color:",secondary,
    ";'>low</span> risk passing profiles </span>"), 
    subtitle = "Data from Infogol, Domestic Leagues 2019/20") +
  xlab("Pass Completion (%)") +
  ylab("Expected Assists Per 90") +
  annotation_custom(logo, xmin = 89, xmax = 90.5, ymin = 0.85, ymax = 0.98) +
  coord_cartesian(clip = "off") 
```

