```
library(topics)
save_dir = '.'
dtm <- topicsDtm(
  data = dep_wor_data$Depword, 
  save_dir = save_dir)

model <- topicsModel(
  dtm = dtm,
  num_topics = 20, save_dir = save_dir)

preds <- topicsPreds(
  model = model,
  data = dep_wor_data$Depword, save_dir = save_dir)

test <- topicsTest(
  data = dep_wor_data,
  model = model,
  preds = preds,
  x_variable = "PHQ9tot",
  save_dir = save_dir)

topicsPlot(
 model = model,
 test = test,
 scatter_legend_topic_n = TRUE, save_dir = save_dir)
```
>Error in `ggplot2::sym()`:
>! Can't convert `NULL` to a symbol.
