# README

# Análise de Dados do Spotify

Este repositório contém um script Python para análise de dados de músicas do Spotify. O objetivo é limpar e analisar o dataset, removendo duplicatas, corrigindo inconsistências, tratando valores ausentes e removendo outliers. O dataset utilizado pode ser encontrado [aqui](https://www.kaggle.com/datasets/vitoriarodrigues/spotifycsv-file-modified-for-data-cleaning).

## Requisitos de Instalação

Para rodar este script, você precisará instalar os seguintes pacotes Python:

```bash
pip install pandas
pip install numpy
pip install matplotlib
pip install seaborn
```

## Estrutura do Código

### Importando as Bibliotecas

O código começa importando os pacotes necessários para a análise:

```python
import pandas as pd # para ler, visualizar e printar informações do dataframe
import matplotlib.pyplot as plt # para construir e customizar gráficos
import seaborn as sns # para visualizar gráficos
import numpy as np # numpy porque é sempre útil importar numpy
```

### Lendo e Visualizando o DataFrame

O dataset é importado de um arquivo CSV e ordenado pela popularidade das músicas:

```python
df = pd.read_csv('spotify.csv', index_col=0) # Definimos que o arquivo ficará guardado no nome "df"
df.sort_values('song_popularity', ascending=False, inplace=True) # Ordena as músicas por popularidade
df.head(15) # Mostra as primeiras 15 linhas do dataframe
```

### Checando as Informações do DataFrame

Visualizamos as primeiras linhas e obtemos informações e descrições detalhadas do dataframe:

```python
df.head() # Visualiza o começo do dataframe
df.info() # Obtém informações do dataframe
df.describe() # Descrição mais detalhada
df_estatistica = df.describe() # Armazena as estatísticas descritivas em uma variável
```

### Duplicatas

Identificamos e removemos duplicatas no dataframe:

```python
duplicados = df[df.duplicated(keep='first')] # Identifica duplicatas
print(duplicados) # Imprime duplicatas encontradas
df.drop_duplicates(keep='first', inplace=True) # Remove duplicatas mantendo a primeira ocorrência
```

### Inconsistências

Removemos unidades desnecessárias das colunas especificadas:

```python
def remove_units(DataFrame, columns, what):
    for col in columns:
        DataFrame[col] = DataFrame[col].str.strip(what)
 
remove_units(df, ['acousticness', 'danceability'], 'mol/L')
remove_units(df, ['song_duration_ms', 'acousticness'], 'kg')
```

Substituímos valores inconsistentes por NaN:

```python
type(np.nan)
df = df.replace(['nao_sei'], np.nan)
df['key'] = df['key'].replace([0.177], np.nan)
df['audio_mode'] = df['audio_mode'].replace(['0.105'], np.nan)
df['speechiness'] = df['speechiness'].replace(['0.nao_sei'], np.nan)
df['time_signature'] = df['time_signature'].replace(['0.7', '2800000000'], np.nan)
```

### Tipos de Dados

Convertendo colunas para tipos apropriados:

```python
numerical_cols = ['song_duration_ms', 'acousticness', 'danceability',
                  'energy', 'instrumentalness', 'liveness', 'loudness',
                  'speechiness', 'tempo', 'audio_valence']
 
categorical_cols = ['song_popularity', 'key', 'audio_mode', 'time_signature']
 
def to_type(DataFrame, columns, type):
    for col in columns:
        DataFrame[col] = DataFrame[col].astype(type)
 
to_type(df, numerical_cols, 'float')
to_type(df, categorical_cols, 'category')
```

### Outliers

Função para excluir outliers das colunas especificadas:

```python
def exclui_outliers(DataFrame, col_name):
  intervalo = 2.7 * DataFrame[col_name].std()
  media = DataFrame[col_name].mean()
  DataFrame.loc[DataFrame[col_name] < (media - intervalo), col_name] = np.nan
  DataFrame.loc[DataFrame[col_name] > (media + intervalo), col_name] = np.nan
  
for col in numerical_cols:
  exclui_outliers(df, col)
```

### Dados Faltantes

Preenchendo valores ausentes com a moda ou a média das colunas:

```python
for column in ['acousticness', 'liveness', 'speechiness']:
    df[column].fillna(df[column].mode()[0], inplace=True)

for column in ['song_duration_ms', 'danceability', 'energy', 'loudness', 'audio_valence']:
    df[column].fillna(df[column].mean(), inplace=True)
    
df.dropna(inplace=True) # Remove linhas restantes com valores ausentes
```

## Conclusão

Este script fornece um guia detalhado para a limpeza e preparação de dados de músicas do Spotify. Inclui etapas para lidar com duplicatas, inconsistências, tipos de dados, outliers e dados faltantes.

## Como Utilizar

1. Clone o repositório:
   ```bash
   git clone https://github.com/seu-usuario/nome-do-repositorio.git
   ```

2. Navegue até o diretório do projeto:
   ```bash
   cd nome-do-repositorio
   ```

3. Instale os pacotes necessários:
   ```bash
   pip install -r requirements.txt
   ```

4. Execute o script de análise:
   ```bash
   python script_analise.py
   ```

Para quaisquer dúvidas ou sugestões, sinta-se à vontade para abrir uma issue ou enviar um pull request.

---

Espero que este guia ajude você a entender melhor o processo de limpeza e análise de dados 
