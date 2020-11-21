# nyc-taxi-data
Case study: A new ride sharing company X is looking to start up a rival service to Uber or Lyft in NYC. They are interested in using the Taxi Data to get insight into the transportation sector in New York. I need to get to know the data and, additionally, build a predictive model to suggest tip amount for customers.

## Issues with data:
1. **Data is large (3.9 GB).** 12 GB of RAM in Google Colab was not enough to preprocess the whole data, the machine was crashing. Solution: load the data in chunks using `pd.read_csv(file, chunksize=1000000)` and preprocess chunk by chunk, concatenating them together into one preprocessed dataframe in the end. Refer to [this article](https://towardsdatascience.com/why-and-how-to-use-pandas-with-large-data-9594dda2ea4c) for more info.
2. To reduce the amount of RAM taken by the data even more, set the variable types to the ones that take up less space. For instance, if the `payment_type` only contains 6 possible values, it makes sense to set the type to `int8` rather than `int64` that takes 8 times more memory.
3. **Data contains outliers and mistakes.** These are easily detected and removed using quantiles. I removed all the data that is below 0.001-th quantile and above 0.999-th quantile, 0.2% in total that cleaned up the data a lot. 
4. **Some features are not very descriptive.** Datetimes of the rides are hard to interpret for both humans and our model. I engineered two new features manually: time spent in a ride (`min_spent`) and hour of the day where the ride took place (`time_hour`). Additionally, I used `fastai`'s `add_datepart()` function to extract more features from this data, like which day of the week it is, whether it is a holiday, beginning or end of the month, etc.

## Summary:
- 90% of the revenues come from the rides that take place inside the New York City. The second most popular destination is JFK airport. 
- The hours with the highest demand are from 6 p.m. to 10 p.m.

![image](https://drive.google.com/uc?export=view&id=1GIJxQy_0Y8PmJFqAA_P2UEYLfBFVN0lk)

- Negotiated fares turn out to bring most revenues by mile travelled. However, they happen very seldom and are hardly controllable. The most efficient solution would be to concentrate on offering rides in the city, they bring $5.85 per mile on average, while trips to the airport bring only $3.70 per mile travelled.
- 64% of all the revenues came from rides with only 1 passenger. A car that takes up to 3 people would cover 90% of the demand.

![image](https://drive.google.com/uc?export=view&id=14uM1vsbScmKhGsTtATASyYzvZmdKxkkf)

- **IDEA:** Rides with only one passenger and inside the city (therefore, probably without lots of baggage) are most common. It might be a good idea to offer rides with **motorbikes**: While taking only 1 passenger max., they consume less fuel and are more flexible in the busy city-setting.

## Random Forest model for tip suggestions:
- Root Mean Squared Error was chosen as the metric for measuring model performance.
- RMSE of 0.31 on validation data was achieved. 
- Most important features that predict the value of the tip are: the total amount spent and payment method.

![image](https://drive.google.com/uc?export=view&id=1B3nbKnvkDqFMXAhZ_zBUuIEwtPImtVQ-)
- The hour of the day as well as the day of the week resulted as not important for predicting the tip amount. (My hypothesis was that people would pay more on, say, Friday than on Monday because they would be more cheerful because of the weekend. It was disproven)

## Further model improvement and deployment
- More data should be gathered on the payment method. The model predicts high tips when people pay by card simply because data on tips paid in cash was not captured. The suggestion to improve the model would be for the company X to gather that data by, for example, motivating drivers to report on their tips in cash saying that this data will help themselves to earn more tips in the future thanks to better model performance.
- `treeinterpreter` and `waterfall_charts` are two great tools to use in production. Thanks to relative simplicity of the Random Forest algorithm, it can be explained pretty easily, allowing both the customer and the company to see how exactly the model made its prediction, showing how each feature contributed to the predicted value.

![image](https://drive.google.com/uc?export=view&id=1apxHM2NjQEgOSoJQBpA6Yv9RUpoXxEOV)
