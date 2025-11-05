# Formula 1 Point Finisher Prediction

**Background & Purposes**

F1 or Formula One is an open wheel racing organized by Fédération Internationale de l'Automobile (FIA). Each season consists of multiple races, known as Grand Prix and helt at circuits around the world. Drivers and teams (called constructors) compete for the World Drivers and Constructors Championships. Since 2010, only the top 10 finishers earn points. The competition for the 2025 World Drivers Championship is extremely tight, making the prediction of point-scoring (Top 10) positions crucial for understanding championship dynamics and team performance.

This project aims to build a machine learning model that can be predict whether a Formula 1 driver will finish in top 10 in a Grand Prix. This model uses historical race, driver, and constructor data, focusing on the modern era (2014 onward) to improve accuracy. However, this model ignores factors such as pit stop history, tire management, weather conditions, grid penalties, and team orders. **Therefore, the predictions may not fully reflect the real world dynamic of Formula 1 races.**

The Dataset I've been using is from kaggle

**Key Features:**
  - Prediction target: binary classification, (1) if finished at the top 10 and (0) if not
  - Data Era : Focused on the 2014-2015 for performance relevance
  - Model : Using a RandomForestClassifier to handle non linear relationship in the data
  - Optimization : Uses GridSearchCV for hyperparameter tuning

**Dataset**
This project uses several interrelated CSV files:
- results.csv: Final race results for each driver
- races.csv: Information about each race including year,circuit, and round
- drivers.csv: Driver details
- driver_standings.csv: Driver standings after each race
- constructors.csv: Constructor details
- constructors_standings.csv: Constructor standings after each race
- constructor_results.csv: Constructor point per race

**Techstack used**
- Python 3.11.5
- Pandas
- NumPy
- Scikit-learn (RandomForest,GridSearchCV,OneHotEncoder)
- Matplotlib
- Seaborn
- Jupyter Notebook
  
**Workflow**
1. Data loading and cleaning
- Data from 7 csv files are loaded into pandas DataFrames
- The results and races datasets are merged
- Dropped irrevelant data such as drivers DNF
- The target variable 'finished_in_top_10' is created based on the 'position'

2. Feature Engineering
New features are created to capture historical performance of drivers/teams:
- Recent performance: 'driver_avg_points_last_3' (average points from the last 3 races)
- Championship point: 'driver_points_before_race' and 'constructor_points_before_race'
- Circuit performance: 'driver_avg_points_circuit' (average historical points at the same circuit)
- Grid position: 'grid' (qualifying position)

3. Model training and evaluation
- Feature used: 'grid','circuitId','constructorId','driver_points_before_race','constructor_points_before_race','driver_avg_points_last_3','driver_avg_points_circuit'
- Preprocessing: Categorical features (circuitId, constructorId) are one-hot encoded
- Model: rf (RandomForestClassifier) is used
- Tuning: GridSearchCV finds optimal parameters:
          - max_depth: 10
          - min_samples_leaf: 2
          - min_samples_split: 2
          - n_estimators: 150

the model before tuning: 

<img width="611" height="302" alt="image" src="https://github.com/user-attachments/assets/441cfdfa-83a2-4e52-8b5c-e10f603f5f27" />

<img width="539" height="432" alt="image" src="https://github.com/user-attachments/assets/bd82b32e-92ff-457d-94dc-21c832ad1826" />

we can see the model before tuning is kinda biased, the model performs better in identifying class 1 (positive) more than 0 (negative) and the main weakness is the high number of false positive (93), where the model is too optimistic in predicting something as class 1 when it's actually class 0

the model after tuning:

<img width="563" height="304" alt="image" src="https://github.com/user-attachments/assets/272cad02-dda3-4e15-b806-c9b1b5f2634f" />

<img width="539" height="432" alt="image" src="https://github.com/user-attachments/assets/3efd2bb1-e5a2-4f4c-a132-dbc52708f63a" />

The model after tuning shows major improvement by increasing true negatives (106 → 155) and cutting false positive (93 → 44), making it much better at predicting drivers who won't finish in the top 10

**Result**

The model was evaluated on the 2025 test data and achieved **accuracy: 79.45%**

**Feature importance:**

Visualization shows that historical performance and qualifying position are the strongest predictors:

<img width="1042" height="545" alt="image" src="https://github.com/user-attachments/assets/a8b92b81-f99c-470f-8666-0fb904a6a45d" />

1. driver_avg_points_last_3: reflects recent momentum, and the strongest predictor of next race performance.
2. grid: lower grid numbers or front row greatly increase the chance of the finishing in the top 10
3. constructor_points_before_race: indicates team strength and car consistency across the season
4. driver_points_before_race: represents overall season performance and reliability
5. driver_avg_points_circuit: show if the driver is historically strong or a "track specialist" at this circuit

**Prediction Example: the upcoming Brazilian GP 2025 (7-10 November)**

This project concludes with a case study where the model predicts the probability of drivers who will be finished in the top 10 for the Brazilian GP 2025 (based on manually entered performance data)

<img width="1625" height="853" alt="image" src="https://github.com/user-attachments/assets/99e1f476-9d2a-4a90-8659-0b424057675a" />

Top 3 (Podium) Prediction Drivers:
1. Verstappen: 97.26%
2. Leclerc: 95.33%
3. Norris: 94.37%

And once again reminder, **THIS PREDICTIONS MAY NOT FULLY REFLECT THE REAL WORLD DYNAMIC OF FORMULA 1 RACES.**




