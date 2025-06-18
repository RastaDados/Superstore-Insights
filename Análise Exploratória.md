## Exploração Inicial dos Dados

### Carregando os Dados

```python
import pandas as pd

import warnings
warnings.filterwarnings('ignore')

#Carregando o dado com um encoding diferente de UTF-8, estava dando muitos problemas
df = pd.read_csv('dados/Superstore.csv', encoding='ISO-8859-1')

#Visualizando as primeiras linhas
print(df.head())

#Verificando os tipos de dados
print(df.dtypes)
```

![image](https://github.com/user-attachments/assets/0e2eb888-001c-466e-b8ae-c49ec73cf2bf)

### Estatísticas Descritivas

```python
#Estatísticas descritivas
print(df.describe())
```

![image](https://github.com/user-attachments/assets/c11b54ed-58e2-494a-9153-ac9ae81f9965)

### Identificação de Valores Faltantes, Duplicados e Inconsistências

```python
#Verificando se tem valores nulos
print(df.isnull().sum())

#Verificando se tem dados duplicados
print(df.duplicated().sum())

#Verificando se tem inconsistências, outliers, etc
print(df['Sales'].describe())
```

![image](https://github.com/user-attachments/assets/f299dfd4-ee35-479a-9da9-ab7470e2ccef)

### Análise Inicial da Distribuição das Variáveis

```python
import matplotlib.pyplot as plt
import seaborn as sns

#Distribuição de vendas
plt.figure(figsize=(10, 6))
sns.histplot(df['Sales'], bins=30, kde=True)
plt.title('Distribuição de Vendas')
plt.xlabel('Vendas')
plt.ylabel('Frequência')
plt.show()

#Distribuição de lucro
plt.figure(figsize=(10, 6))
sns.histplot(df['Profit'], bins=30, kde=True)
plt.title('Distribuição de Lucro')
plt.xlabel('Lucro')
plt.ylabel('Frequência')
plt.show()
```

![image](https://github.com/user-attachments/assets/8b2d5e24-0115-40ff-acfe-af71e2342de8)

![image](https://github.com/user-attachments/assets/9843f31f-9d85-4326-8ca2-dc18e6ef90c5)

## Limpeza e Preparação dos Dados

### Tratamento de Valores Nulos e Duplicados

```python
#Removendo valores que estão duplicados
df.drop_duplicates(inplace=True)

#Preenchendo os valores nulos, só se for necessário
df.fillna(method='ffill', inplace=True)
```

#### Transformações Necessárias (Normalização, Encoding de Variáveis Categóricas, etc.)

```python
from sklearn.preprocessing import LabelEncoder

#Convertendo as datas para valores de datetime
df['Order Date'] = pd.to_datetime(df['Order Date'])
df['Ship Date'] = pd.to_datetime(df['Ship Date'])

#Encoding de variáveis categóricas
label_encoders = {}
categorical_columns = ['Region', 'Category', 'Sub-Category', 'Customer Name', 'Product Name']

for column in categorical_columns:
    le = LabelEncoder()
    df[column] = le.fit_transform(df[column])
    label_encoders[column] = le
```

### Criação de Novas Features, se Relevante

```python
#Criando a coluna de mês e ano para ajudar na visualização dos dados
df['Order Month'] = df['Order Date'].dt.month
df['Order Year'] = df['Order Date'].dt.year
```

## Análise Exploratória Detalhada

### Gráficos e Visualizações Relevantes (Distribuições, Correlações, Comparações)

```python
#Selecionando apenas as colunas numéricas
numeric_cols = df.select_dtypes(include=['number']).columns

#Correlação entre variáveis numéricas
plt.figure(figsize=(12, 8))
corr_matrix = df[numeric_cols].corr()
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm')
plt.title('Matriz de Correlação')
plt.show()

#Distribuição de vendas por ano
plt.figure(figsize=(12, 6))
sns.lineplot(data=df, x='Order Year', y='Sales')
plt.title('Vendas por Ano')
plt.xlabel('Ano')
plt.ylabel('Vendas')
plt.show()

#Distribuição de vendas por mês
plt.figure(figsize=(12, 6))
sns.lineplot(data=df, x='Order Month', y='Sales')
plt.title('Vendas por Mês')
plt.xlabel('Mês')
plt.ylabel('Vendas')
plt.show()

#Categorias de produtos mais vendidas
plt.figure(figsize=(12, 6))
sns.barplot(x='Category', y='Sales', data=df)
plt.title('Vendas por Categoria de Produto')
plt.xlabel('Categoria')
plt.ylabel('Vendas')
plt.show()
```

![image](https://github.com/user-attachments/assets/87c89cb0-e897-4cd6-8184-615cfaa493b0)

![image](https://github.com/user-attachments/assets/10630c2a-3757-4ba2-a90c-9ea31f8b72c8)

![image](https://github.com/user-attachments/assets/bd662f92-89e5-4ca1-b464-cc96f05bdee9)

![image](https://github.com/user-attachments/assets/a220685d-19ae-435d-9498-0cac10699e14)

### Análise Estatística Básica (Média, Mediana, Desvio Padrão, Correlação)

```python
#Estatísticas básicas
print(df[numeric_cols].agg(['mean', 'median', 'std']))
```

![image](https://github.com/user-attachments/assets/c0a79120-5ac8-4bc7-a9b0-a94be1234beb)
