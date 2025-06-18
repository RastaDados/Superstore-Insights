### Dashboard Utilizando o Dash

```python
import pandas as pd
import numpy as np

#Carregando o dataset
df = pd.read_csv('dados/Superstore.csv', encoding='ISO-8859-1')

#Convertendo datas para o formato correto de datetime
df['Order Date'] = pd.to_datetime(df['Order Date'])
df['Ship Date'] = pd.to_datetime(df['Ship Date'])

#Criando a coluna de mês e ano para visualizações
df['Order Month'] = df['Order Date'].dt.month
df['Order Year'] = df['Order Date'].dt.year

#Função para detectar e tratar outliers usando IQR
def remove_outliers_iqr(df, column):
    Q1 = df[column].quantile(0.25)
    Q3 = df[column].quantile(0.75)
    IQR = Q3 - Q1
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    df_filtered = df[(df[column] >= lower_bound) & (df[column] <= upper_bound)]
    return df_filtered

#Tratando os outliers nas colunas numéricas relevantes
df = remove_outliers_iqr(df, 'Sales')
df = remove_outliers_iqr(df, 'Quantity')
df = remove_outliers_iqr(df, 'Discount')
df = remove_outliers_iqr(df, 'Profit')
```

```python
import dash
from dash import dcc, html
from dash.dependencies import Input, Output
import plotly.express as px

#Inicializando o app
app = dash.Dash(__name__)

#Layout do app
app.layout = html.Div([
    html.H1("Dashboard de Previsão de Vendas"),
    
    dcc.Dropdown(
        id='category-dropdown',
        options=[{'label': category, 'value': category} for category in df['Category'].unique()],
        value='Technology'
    ),
    
    dcc.Graph(id='sales-by-month'),
    dcc.Graph(id='sales-by-year'),
    dcc.Graph(id='quantity-by-month'),
    dcc.Graph(id='profit-by-category'),
    dcc.Graph(id='discount-by-category')
])

#Callback para atualizar o gráfico de vendas por mês
@app.callback(
    Output('sales-by-month', 'figure'),
    [Input('category-dropdown', 'value')]
)
def update_sales_by_month(selected_category):
    filtered_df = df[df['Category'] == selected_category]
    monthly_sales = filtered_df.groupby('Order Month')['Sales'].sum().reset_index()
    fig = px.bar(monthly_sales, x='Order Month', y='Sales', title=f'Vendas por Mês - {selected_category}', labels={'Order Month': 'Mês', 'Sales': 'Vendas'})
    fig.update_xaxes(type='category')
    return fig

#Callback para atualizar o gráfico de vendas por ano
@app.callback(
    Output('sales-by-year', 'figure'),
    [Input('category-dropdown', 'value')]
)
def update_sales_by_year(selected_category):
    filtered_df = df[df['Category'] == selected_category]
    yearly_sales = filtered_df.groupby('Order Year')['Sales'].sum().reset_index()
    fig = px.bar(yearly_sales, x='Order Year', y='Sales', title=f'Vendas por Ano - {selected_category}', labels={'Order Year': 'Ano', 'Sales': 'Vendas'})
    fig.update_xaxes(type='category')
    return fig

#Callback para atualizar o gráfico de quantidade por mês
@app.callback(
    Output('quantity-by-month', 'figure'),
    [Input('category-dropdown', 'value')]
)
def update_quantity_by_month(selected_category):
    filtered_df = df[df['Category'] == selected_category]
    monthly_quantity = filtered_df.groupby('Order Month')['Quantity'].sum().reset_index()
    fig = px.bar(monthly_quantity, x='Order Month', y='Quantity', title=f'Quantidade Vendida por Mês - {selected_category}', labels={'Order Month': 'Mês', 'Quantity': 'Quantidade'})
    fig.update_xaxes(type='category')
    return fig

#Callback para atualizar o gráfico de lucro por categoria
@app.callback(
    Output('profit-by-category', 'figure'),
    [Input('category-dropdown', 'value')]
)
def update_profit_by_category(selected_category):
    filtered_df = df[df['Category'] == selected_category]
    profit_by_category = filtered_df.groupby('Category')['Profit'].sum().reset_index()
    fig = px.bar(profit_by_category, x='Category', y='Profit', title='Lucro por Categoria', labels={'Category': 'Categoria', 'Profit': 'Lucro'})
    return fig

#Callback para atualizar o gráfico de desconto por categoria
@app.callback(
    Output('discount-by-category', 'figure'),
    [Input('category-dropdown', 'value')]
)
def update_discount_by_category(selected_category):
    filtered_df = df[df['Category'] == selected_category]
    discount_by_category = filtered_df.groupby('Category')['Discount'].mean().reset_index()
    fig = px.bar(discount_by_category, x='Category', y='Discount', title='Desconto Médio por Categoria', labels={'Category': 'Categoria', 'Discount': 'Desconto Médio'})
    return fig

#Executar o app (porta padrão geralmente é a 8050)
if __name__ == '__main__':
    app.run_server(debug=True)
```

![01](https://github.com/user-attachments/assets/552da499-7be4-4d30-ab20-afb505964fea)

![02](https://github.com/user-attachments/assets/ce300368-a996-43eb-ba35-bf7e4cfc9cc5)

![03](https://github.com/user-attachments/assets/48996f49-b2a2-4774-993c-7fc233d64db0)

![04](https://github.com/user-attachments/assets/b8e9b8c1-cf80-470d-889e-941538d0b5b2)

![05](https://github.com/user-attachments/assets/0431bb37-ae2e-49ec-a545-7d684b0b5345)


