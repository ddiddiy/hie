```python
import pandas as pd
import numpy as np
from scipy import stats
from sklearn.preprocessing import *

data = {
  'age': [
    25, 30, 35, 40, 45, np.nan, np.nan, 60, np.nan, 70
  ],
  'salary': [
    50000, 60000, 70000, 80000, 90000, 100000, 110000, 120000, 130000, np.nan
  ],
  'dept': [
    'HR', 'IT', 'Finance', 'HR', 'IT', None, 'HR', 'IT', 'Finance', None
  ]
}

df =  pd.DataFrame(data)
print(df)

df['age'] = df['age'].fillna(df['age'].median())
df['salary'] = df['salary'].fillna(df['salary'].median())
df['dept'] = df['dept'].fillna(df['dept'].mode())
print(df)

def outlier_iqr(col):
  q1 = col.quantile(0.25)
  q3 = col.quantile(0.75)
  iqr = q3 - q1
  ll = q1 - 1.5 * iqr
  ul = q3 + 1.5 * iqr
  return col.clip(ll, ul)

df['age'] = outlier_iqr(df['age'])
df['salary'] = outlier_iqr(df['salary'])
print(df)

scaler = StandardScaler()
df[['age_scaled', 'salary_scaled']] = scaler.fit_transform(df[['age', 'salary']])
print(df)

minmax = MinMaxScaler()
df[['age_norm', 'salary_norm']] = minmax.fit_transform(df[['age', 'salary']])
print(df) 
```

```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import seaborn as sns 
from sklearn.impute import SimpleImputer
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import *

np.random.seed(42)
n = 200

data = {
  'cust_age': np.random.randint(18, 70, n),
  'cust_income': np.random.randint(30000, 150000, n).astype(int),
  'gender': np.random.choice(['M', 'F'], n),
  'city': np.random.choice(['NY', 'LA', 'Chicago', 'Houston', 'Denver', 'Seattle', 'DC'], n),
  'purchase_amt': np.random.randint(500, 50000, n).astype(int),
  'visit_freq': np.random.randint(1, 20, n)
}

df = pd.DataFrame(data)
print(df.head())

for col in ['cust_income', 'purchase_amt']:
  df.loc[df.sample(frac=0.1).index, col] = np.nan

df.loc[df.sample(frac=0.1).index, 'cust_income'] = df['cust_income'] * 5

print(df.head())
print(df.isnull().sum())

imputer = SimpleImputer(strategy='median')
df['cust_income'] = imputer.fit_transform(df[['cust_income']])
df['purchase_amt'] = imputer.fit_transform(df[['purchase_amt']])
print(df.isnull().sum())
print(df.head())

plt.title('Customer Income with Outliers')
plt.xlabel('Annual Income')
sns.boxplot(data=df, x='cust_income')
plt.show()

plt.figure(figsize=(10, 6))
sns.boxplot(data=df, x='purchase_amt')
plt.show()

def outliers_iqr(col):
  q1 = col.quantile(0.25)
  q3 = col.quantile(0.75)
  iqr = q3 - q1
  ll = q1 - 1.5 * iqr
  ul = q3 + 1.5 * iqr
  return col.clip(ll, ul)

df['cust_income'] = outliers_iqr(df['cust_income'])
plt.title('Customer Income with Outliers')
plt.xlabel('Annual Income')
sns.boxplot(data=df, x='cust_income')
plt.show()

le = LabelEncoder()
df['gender'] = le.fit_transform(df['gender'])
df['city'] = le.fit_transform(df['city'])
print(df.head())

scaler = StandardScaler()
df[['cust_age', 'cust_income', 'purchase_amt', 'visit_freq']] = scaler.fit_transform(df[['cust_age', 'cust_income', 'purchase_amt', 'visit_freq']])
print(df.head())

plt.hist(df['purchase_amt'], bins=20, edgecolor='k')
plt.show()

sns.heatmap(df.corr(), annot=True)
plt.show()

print(df.groupby('gender')['purchase_amt'].mean())
print(df.groupby('city')['purchase_amt'].mean())
```

Prac 3 - 1
```python
from sklearn.datasets import load_iris
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np

data = load_iris()
df = pd.DataFrame(data.data, columns=data.feature_names)
print(df)

print(data.keys())
print(data.data.shape)
print(data.feature_names)
print(data.target_names)
print(data.DESCR)
print(data.filename)
print(data.data_module)

df['target'] = data.target
print(df)

corr_matrix = df.corr()
print(corr_matrix)

sns.heatmap(corr_matrix, annot=True)
plt.title("Corr Heatmap")
plt.show()

strong_corr = df.corr().abs()
strong_corr = strong_corr[strong_corr > 0.8]
print(strong_corr)

plt.figure()
plt.imshow(strong_corr)
plt.colorbar()
plt.xticks(
  range(len(strong_corr)),
  strong_corr.columns,
  rotation=90
)
plt.yticks(
  range(len(strong_corr)),
  strong_corr.columns
)

for i in range(len(strong_corr.columns)):
  for j in range(len(strong_corr.columns)):
    value = strong_corr.iloc[i, j]
    if not np.isnan(value):
      plt.text(j, i, round(value, 2), ha='center', va='center')
plt.title("Strong Corr (>0.8)")
plt.tight_layout()
plt.show()

upper = corr_matrix.where(
  np.triu(np.ones(corr_matrix.shape), k=1).astype(bool)
)

to_drop = [column for column in upper.columns if any(upper[column] > 0.8)]
df_selected = df.drop(columns=to_drop)

print(to_drop)
print(df_selected)
```

Prac 3 - 2
```python
from sklearn.datasets import load_iris
from sklearn.feature_selection import SelectKBest, chi2
import pandas as pd

iris = load_iris()
x = pd.DataFrame(iris.data, columns=iris.feature_names)
y = iris.target

selector = SelectKBest(score_func=chi2, k=2)
x_new = selector.fit_transform(x, y)
selected_features = x.columns[selector.get_support()]
print(selected_features)

score = pd.Series(selector.scores_, index=x.columns)
print(score)
```

Forward Selection
```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.linear_model import LinearRegression
import pandas as pd
import numpy as np

x, y = load_iris(return_X_y=True)
model = LinearRegression()
selected_features = []
remaining_features = list(range(x.shape[1]))
while remaining_features:
  scores = []
  for feature in remaining_features:
    temp_feat = selected_features+[feature]
    score = cross_val_score(model, x[:, temp_feat], y, cv=5, scoring='r2').mean()
    scores.append(score)
  best_feat = remaining_features[np.argmax(scores)]
  selected_features.append(best_feat)
  remaining_features.remove(best_feat)
  print(f"selected features: {selected_features}")
```

Backward Selection
```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.linear_model import LinearRegression
import pandas as pd
import numpy as np
x, y = load_iris(return_X_y=True)
model=LinearRegression()
selected_features=[]
features = list(range(x.shape[1]))
while len(features) > 1:
  scores=[]
  for feature in features:
    temp_feat = features.copy()
    temp_feat.remove(feature)
    score = cross_val_score(model, x[:, temp_feat], y, cv=5, scoring='r2').mean()
    scores.append(score)
  worst_feat = features[np.argmax(scores)]
  features.remove(worst_feat)
  print(f"remaining_features: {features}")
```

Linear Regression
```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score
import pandas as pd
import numpy as np

x, y = load_iris(return_X_y=True)
x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)
model = LinearRegression()
model.fit(x_train, y_train)
print(f"Model Coef: {model.coef_}")
print(f"Model Intercept: {model.intercept_}")

y_pred = model.predict(x_test)
print(f"Predictions: {y_test, y_pred}")
print(f"R2 Score: {r2_score(y_test, y_pred)}")
```

sst, ssr, sse
```python
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score

x = pd.DataFrame({
  'x1': [1, 2, 3, 4, 5],
  'x2': [12, 14, 16, 18, 20]
})
y = pd.DataFrame({
  'y': [13, 16, 19, 22, 25]
})

model = LinearRegression()
model.fit(x, y)
print(f"Model Coef: {model.coef_}")
print(f"Model Intercept: {model.intercept_}")
y_pred = model.predict(x)
print(f"Predictions: {y_pred}")

r2 = r2_score(y, y_pred)
print(f"R2 Score: {r2}")

sst = np.var(y, ddof=0) * len(y)
ssr = r2 * sst
sse = sst - ssr
print(f"SST: {sst}")
print(f"SSR: {ssr}")
print(f"SSE: {sse}")
```

cm, tn, fp, fn, tp
```python
from sklearn.metrics import *
import matplotlib.pyplot as plt

y_true = [1, 0, 1, 1, 0, 1, 0, 0]
y_pred = [1, 0, 1, 0, 0, 1, 1, 0]

cm = confusion_matrix(y_true, y_pred)
print(f"Confusion Matrix: {cm}")

disp = ConfusionMatrixDisplay(confusion_matrix=cm, display_labels=['class 0', 'class 1'])
disp.plot(cmap=plt.cm.Blues)
plt.show()

tn, fp, fn, tp = cm.ravel()
print(f"True Negatives: {tn}")
print(f"False Positives: {fp}")
print(f"False Negatives: {fn}")
print(f"True Positives: {tp}")

precision = tp / (tp + fp) 
recall = tp / (tp + fn)
f1 = 2 * (precision * recall) / (precision + recall)
accuracy = (tp + tn) / len(y_true)
print(f"Precision: {precision}")
print(f"Recall: {recall}")
print(f"F1 Score: {f1}")
print(f"Accuracy: {accuracy}")
```

knn
```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import (accuracy_score, precision_score, recall_score, f1_score, confusion_matrix, classification_report)
from sklearn.preprocessing import StandardScaler

iris = load_iris()
x = iris.data
y = iris.target

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.3, random_state=42)

scaler = StandardScaler()
x_train = scaler.fit_transform(x_train)
x_test = scaler.transform(x_test)

knn = KNeighborsClassifier(n_neighbors=5)
knn.fit(x_train, y_train)
y_pred = knn.predict(x_test)
accr = accuracy_score(y_test, y_pred)
prec = precision_score(y_test, y_pred, average='weighted')
rec = recall_score(y_test, y_pred, average='weighted')
f1 = f1_score(y_test, y_pred, average='weighted')
cm = confusion_matrix(y_test, y_pred)

sns.heatmap(cm, annot=True, fmt='d', cmap='Blues')
plt.show()
print(f"Accuracy: {accr}")
print(f"Precision: {prec}")
print(f"Recall: {rec}")
print(f"F1 Score: {f1}")
print(f"Confusion Matrix: {cm}")
print(classification_report(y_test, y_pred, target_names=iris.target_names))
```

Logi
```python
from sklearn.linear_model import LogisticRegression, LinearRegression
from sklearn.metrics import *
from sklearn.model_selection import train_test_split
import numpy as np
import matplotlib.pyplot as plt

x = np.array([[1], [2], [3], [4], [5]])
y = np.array([0, 0, 0, 1, 1])

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)
model = LogisticRegression()
model1 = LinearRegression()
model.fit(x_train, y_train)
model1.fit(x_train, y_train)
y_pred = model.predict(x_test)
y_pred1 = model1.predict(x_test)
print(f"Logistic Regression Predictions: {y_pred}")
print(f"Logistic Regression Accuracy: {accuracy_score(y_test, y_pred)}")

x_curve = np.linspace(1, 5, 100).reshape(-1, 1)
y_curve = model.predict_proba(x_curve.reshape(-1, 1))[:, 1]
y_curve1 = model1.predict(x_curve)

plt.scatter(x, y)
plt.plot(x_curve, y_curve, color='red', label='Logistic Regression')
plt.scatter(x, y)
plt.plot(x_curve, y_curve1, color='blue', label='Linear Regression')
plt.show()

accr = accuracy_score(y_test, y_pred)
prec = precision_score(y_test, y_pred)
print(f"Accuracy: {accr}")
print(f"Precision: {prec}")
print(f"Classification Report:\n{classification_report(y_test, y_pred)}")
print(model.coef_)
print(model.intercept_)
```

decision tree
```python
import pandas as pd
from sklearn.tree import DecisionTreeClassifier, export_text
from sklearn.metrics import *
from sklearn.preprocessing import LabelEncoder, OrdinalEncoder

cols_names = ['outlook', 'temp', 'humidity', 'windy', "play"]
csv = pd.read_csv('Book1.csv')
data = csv.values
x = data[:, :-1].astype(str)
y = data[:, -1].astype(str)

print(f"input: {x.shape}")
print(f"output: {y.shape}")

ordianl = OrdinalEncoder()
le = LabelEncoder()
x = ordianl.fit_transform(x)
y = le.fit_transform(y)

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)
x_train = ordianl.fit_transform(x_train)
y_train = le.fit_transform(y_train)

model = DecisionTreeClassifier()
model.fit(x_train, y_train)
y_pred = model.predict(x_test)
print(f"Predictions: {y_pred}")
print(f"Accuracy: {accuracy_score(y_test, y_pred)}")
print(f"Classification Report:\n{classification_report(y_test, y_pred)}")
print(f"Confusion Matrix: {confusion_matrix(y_test, y_pred)}")
text_representation = export_text(model)
print(text_representation)
```

tree iris
```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.metrics import *
from sklearn.tree import DecisionTreeClassifier, export_text

iris = load_iris()
x = iris.data
y = iris.target

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)

model = DecisionTreeClassifier()
model.fit(x_train, y_train)
y_pred = model.predict(x_test)
print(f"Predictions: {y_pred}")
print(f"Accuracy: {accuracy_score(y_test, y_pred)}")
print(f"Classification Report:\n{classification_report(y_test, y_pred)}")
print(f"Confusion Matrix: {confusion_matrix(y_test, y_pred)}")
text_representation = export_text(model, feature_names=iris.feature_names)
print(text_representation)
```

tree graph
```python
import pandas as pd
from sklearn.tree import DecisionTreeClassifier, export_text, plot_tree
from sklearn.metrics import *
from sklearn.preprocessing import LabelEncoder, OrdinalEncoder

cols_names = ['outlook', 'temp', 'humidity', 'windy', "play"]
csv = pd.read_csv('Book1.csv')
data = csv.values
x = data[:, :-1].astype(str)
y = data[:, -1].astype(str)

print(f"input: {x.shape}")
print(f"output: {y.shape}")

ordianl = OrdinalEncoder()
le = LabelEncoder()
x = ordianl.fit_transform(x)
y = le.fit_transform(y)

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)
x_train = ordianl.fit_transform(x_train)
y_train = le.fit_transform(y_train)

model = DecisionTreeClassifier()
model.fit(x_train, y_train)
y_pred = model.predict(x_test)
print(f"Predictions: {y_pred}")
print(f"Accuracy: {accuracy_score(y_test, y_pred)}")
print(f"Classification Report:\n{classification_report(y_test, y_pred)}")
print(f"Confusion Matrix: {confusion_matrix(y_test, y_pred)}")
text_representation = export_text(model)
print(text_representation)

plt.figure(figsize=(12, 8))
plot_tree(
  model,
  feature_names=cols_names[:-1],
  class_names=le.classes_.astype(str),
  filled=True,
  rounded=True,
  fontsize=8
)
plt.show()
```

tree regressor
```python
import pandas as pd
import sklearn
from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import confusion_matrix
from sklearn.tree import DecisionTreeClassifier, export_text
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score
import matplotlib.pyplot as plt

data = fetch_california_housing()

x = data.data
y = data.target

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)
model = RandomForestRegressor(n_estimators=100, random_state=42)
model.fit(x_train, y_train)

y_pred = model.predict(x_test)
mae = mean_absolute_error(y_test, y_pred)
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)
print(f"Predictions: {y_pred}")
print(f"Mean Absolute Error: {mae}")
print(f"Mean Squared Error: {mse}")
print(f"R2 Score: {r2}")
```

navies
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.metrics import *
from sklearn.preprocessing import LabelEncoder
from sklearn.naive_bayes import GaussianNB

data = pd.read_csv('car_dataset.csv')
print(data.head())
df = pd.DataFrame(data)
le = LabelEncoder()
for col in df.columns:
  df[col] = le.fit_transform(df[col])

x = df.drop('Stolen', axis=1)
y = df['Stolen']

model = GaussianNB()
model.fit(x, y)

y_pred = model.predict(x)
accr = accuracy_score(y, y_pred)
prec = precision_score(y, y_pred, average='weighted')
rec = recall_score(y, y_pred, average='weighted')
f1 = f1_score(y, y_pred, average='weighted')
cm = confusion_matrix(y, y_pred)

print(f"Accuracy: {accr}")
print(f"Precision: {prec}")
print(f"Recall: {rec}")
print(f"F1 Score: {f1}")
print(f"Confusion Matrix:\n{cm}")
print(classification_report(y, y_pred))
```

svc
```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.svm import SVC
from sklearn.metrics import *
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

iris = load_iris()
x = iris.data[:, :2]
y = iris.target

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)

model = SVC(kernel='linear')
model.fit(x_train, y_train)
y_pred = model.predict(x_test)

print(f"Predictions: {y_pred}")
print(f"Confusion Matrix: {confusion_matrix(y_test, y_pred)}")
print(f"Classification Report:\n{classification_report(y_test, y_pred)}")

h = 0.2
x_min, x_max = x[:, 0].min() - 1, x[:, 0].max() + 1
y_min, y_max = x[:, 1].min() - 1, x[:, 1].max() + 1

xx, yy = np.meshgrid(np.arange(x_min, x_max, h), np.arange(y_min, y_max, h))
z = model.predict(np.c_[xx.ravel(), yy.ravel()])
z = z.reshape(xx.shape)
plt.contourf(xx, yy, z, alpha=0.3)
plt.scatter(x[:, 0], x[:, 1], c=y, edgecolors='k', marker='o')
plt.xlabel(iris.feature_names[0])
plt.ylabel(iris.feature_names[1])
plt.title("SVM Decision Boundary")

```

elbow
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.datasets import load_iris
from sklearn.cluster import KMeans
from scipy.cluster.hierarchy import dendrogram, linkage
from sklearn.preprocessing import StandardScaler

iris = load_iris()
x = iris.data
feat = iris.feature_names
y = iris.target
df = pd.DataFrame(x, columns=feat)

scaler = StandardScaler()
x_scaled = scaler.fit_transform(x)
df_scaled = pd.DataFrame(x_scaled, columns=feat)

wcss = []
for i in range(1, 10):
  kmeans = KMeans(n_clusters=i, n_init=10, random_state=42)
  kmeans.fit(df_scaled)
  wcss.append(kmeans.inertia_)

plt.plot(range(1, 10), wcss, marker='o')
plt.title('Elbow Method')
plt.xlabel('Number of clusters')
plt.show()

kmeans = KMeans(n_clusters=3, n_init=10, random_state=42) 
y_kmeans = kmeans.fit_predict(df_scaled)
plt.scatter(x_scaled[y_kmeans == 0, 0], x_scaled[y_kmeans == 0, 1])
plt.scatter(x_scaled[y_kmeans == 1, 0], x_scaled[y_kmeans == 1, 1])
plt.scatter(x_scaled[y_kmeans == 2, 0], x_scaled[y_kmeans == 2, 1])
plt.scatter(kmeans.cluster_centers_[:, 0], kmeans.cluster_centers_[:, 1], s=100, c='red')
plt.title('K-Means Clustering')
plt.show()

plt.figure()
dendrogram(linkage(df_scaled, method='ward'))
plt.title('Dendrogram')
plt.show()
```

app.py
```python
from flask import Flask, request, jsonify
import pickle

app = Flask(__name__)

model = pickle.load(open('model.pkl', 'rb'))

@app.route('/predict', methods=['POST'])
def predict():
  data = request.json["hrs"]
  prediction = model.predict([[data]])
  return jsonify({'prediction': int(prediction[0])})

if __name__ == "__main__":
  app.run(host='0.0.0.0', port=5000, debug=True)
  
```

train_model.py
```python
import pickle
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression

data = pd.read_csv("student.csv")
x = data[['hrs']]
y = data['results']

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2, random_state=42)

model = LogisticRegression()
model.fit(x_train, y_train)
pickle.dump(model, open('model.pkl', 'wb'))

```

Dcokerfile
```python
FROM python:3.14-slim

WORKDIR /app

COPY /mlops .

RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 5000

CMD [ "python", "app.py" ]
```

