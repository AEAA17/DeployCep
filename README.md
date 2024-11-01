## Controle de Processos - Gráficos de Controle com Streamlit

### Descrição do Código
Este aplicativo em Python, utilizando a biblioteca Streamlit, permite a visualização de gráficos de controle baseados em dados de amostras fornecidos em um arquivo Excel. O usuário pode escolher entre diferentes tipos de gráficos de controle, como Média Vs Amplitude, Média Vs Desvio Padrão e Média Vs CUSUM. O aplicativo também calcula os limites de controle com base em uma tabela de fatores para diferentes tamanhos de amostras.

### Dependências
- `pandas`: Para manipulação de dados.
- `streamlit`: Para criação do aplicativo web.
- `numpy`: Para operações numéricas.
- `plotly`: Para criação de gráficos interativos.

### Código:
```python
import pandas as pd
import streamlit as st
import numpy as np
import plotly.graph_objects as go
```

### Tabela de fatores para diferentes tamanhos de amostras (n)
```
tabela_fatores = {
    2: {'A2': 1.88, 'A3': 2.659, 'B3': 0, 'B4': 3.267, 'D3': 0, 'D4': 3.267},
    3: {'A2': 1.023, 'A3': 1.954, 'B3': 0, 'B4': 2.568, 'D3': 0, 'D4': 2.574},
    4: {'A2': 0.729, 'A3': 1.628, 'B3': 0, 'B4': 2.266, 'D3': 0, 'D4': 2.282},
    5: {'A2': 0.577, 'A3': 1.427, 'B3': 0, 'B4': 2.089, 'D3': 0, 'D4': 2.114},
    6: {'A2': 0.483, 'A3': 1.287, 'B3': 0.03, 'B4': 1.97, 'D3': 0, 'D4': 2.004},
    7: {'A2': 0.419, 'A3': 1.182, 'B3': 0.118, 'B4': 1.882, 'D3': 0.076, 'D4': 1.924},
    8: {'A2': 0.373, 'A3': 1.099, 'B3': 0.185, 'B4': 1.815, 'D3': 0.136, 'D4': 1.864},
    9: {'A2': 0.337, 'A3': 1.032, 'B3': 0.239, 'B4': 1.761, 'D3': 0.184, 'D4': 1.816},
    10: {'A2': 0.308, 'A3': 0.975, 'B3': 0.284, 'B4': 1.716, 'D3': 0.223, 'D4': 1.777},
    11: {'A2': 0.285, 'A3': 0.927, 'B3': 0.321, 'B4': 1.679, 'D3': 0.256, 'D4': 1.744},
    12: {'A2': 0.266, 'A3': 0.886, 'B3': 0.354, 'B4': 1.646, 'D3': 0.283, 'D4': 1.717},
    13: {'A2': 0.249, 'A3': 0.85, 'B3': 0.382, 'B4': 1.618, 'D3': 0.307, 'D4': 1.693},
    14: {'A2': 0.235, 'A3': 0.817, 'B3': 0.406, 'B4': 1.594, 'D3': 0.328, 'D4': 1.672},
    15: {'A2': 0.223, 'A3': 0.789, 'B3': 0.428, 'B4': 1.572, 'D3': 0.347, 'D4': 1.653},
    16: {'A2': 0.212, 'A3': 0.763, 'B3': 0.448, 'B4': 1.552, 'D3': 0.363, 'D4': 1.637},
    17: {'A2': 0.203, 'A3': 0.739, 'B3': 0.466, 'B4': 1.534, 'D3': 0.378, 'D4': 1.622},
    18: {'A2': 0.194, 'A3': 0.718, 'B3': 0.482, 'B4': 1.518, 'D3': 0.391, 'D4': 1.608},
    19: {'A2': 0.187, 'A3': 0.698, 'B3': 0.497, 'B4': 1.503, 'D3': 0.403, 'D4': 1.597},
    20: {'A2': 0.18, 'A3': 0.68, 'B3': 0.51, 'B4': 1.49, 'D3': 0.415, 'D4': 1.585},
    21: {'A2': 0.173, 'A3': 0.663, 'B3': 0.523, 'B4': 1.477, 'D3': 0.425, 'D4': 1.575},
    22: {'A2': 0.167, 'A3': 0.647, 'B3': 0.534, 'B4': 1.466, 'D3': 0.434, 'D4': 1.566},
    23: {'A2': 0.162, 'A3': 0.633, 'B3': 0.545, 'B4': 1.455, 'D3': 0.443, 'D4': 1.557},
    24: {'A2': 0.157, 'A3': 0.619, 'B3': 0.555, 'B4': 1.445, 'D3': 0.451, 'D4': 1.548},
    25: {'A2': 0.153, 'A3': 0.606, 'B3': 0.565, 'B4': 1.435, 'D3': 0.459, 'D4': 1.541},
}
```

### Função para selecionar os fatores com base no tamanho da amostra
```
def get_factors(n):
    return tabela_fatores.get(n, {'A2': 0, 'A3': 0, 'B3': 0, 'B4': 0, 'D3': 0, 'D4': 0})
```
### Configuração do aplicativo Streamlit
```
st.title("Gráficos de Controle")
```
### Upload de arquivo Excel e Criação dos Gráficos 
```
uploaded_file = st.file_uploader("Escolha um arquivo Excel", type="xlsx")

if uploaded_file is not None:
    df = pd.read_excel(uploaded_file)
    # Criando um container com borda
    container = st.container(border=True)
    
    # Colocando as opções e gráficos dentro do container
    with container:
        
        # Opções para seleção
        option = st.selectbox("Escolha uma opção de gráfico:", 
                            ["Média Vs Amplitude", "Média Vs Desvio Padrão", "Média Vs CUSUM"])
        
        if option == "Média Vs Amplitude":
            def calcular_media_r(df):
                st.subheader("Gráfico de Controle - Média")
                n = df.shape[1]
                fatores = get_factors(n)
                A2 = fatores['A2']

                medias_linhas = df.mean(axis=1)
                amplitude_linhas = df.max(axis=1) - df.min(axis=1)

                media_global = medias_linhas.mean()
                amplitude_media = amplitude_linhas.mean()

                lsc_xr = media_global + A2 * amplitude_media
                lic_xr = media_global - A2 * amplitude_media
                
                # Calculando as métricas
                acima_lsc = sum(medias_linhas > lsc_xr)
                abaixo_lic = sum(medias_linhas < lic_xr)

                # Criando os cartões acima do gráfico
                coluna_esquerda, coluna_direita = st.columns([1, 1])
                coluna_esquerda.metric("Amostras acima do LSC", f'{acima_lsc}')
                coluna_direita.metric("Amostras abaixo do LIC", f'{abaixo_lic}')

                # Criando o gráfico de controle
                fig = go.Figure()
                fig.add_trace(go.Scatter(x=list(range(len(medias_linhas))), y=medias_linhas, mode='lines+markers', name='Média das Amostras'))
                fig.add_trace(go.Scatter(x=list(range(len(amplitude_linhas))), y=amplitude_linhas, mode='lines+markers', name='Amplitude das Amostras'))
                fig.add_hline(y=lsc_xr, line_color='red', line_dash='dash', annotation_text="LSC", annotation_position="bottom right")
                fig.add_hline(y=lic_xr, line_color='red', line_dash='dash', annotation_text="LIC", annotation_position="bottom right")
                fig.update_layout(title="Gráfico de Controle - Média Vs Amplitude", xaxis_title="Amostras", yaxis_title="Valores")
                st.plotly_chart(fig)

            calcular_media_r(df)
        
        elif option == "Média Vs Desvio Padrão":
            # Código para o gráfico de Média Vs Desvio Padrão
            def calcular_media_dp(df):
                st.subheader("Gráfico de Controle - Média Vs Desvio Padrão")
                n = df.shape[1]
                fatores = get_factors(n)
                A3 = fatores['A3']

                medias_linhas = df.mean(axis=1)
                desvios_padroes = df.std(axis=1)

                media_global = medias_linhas.mean()
                dp_global = desvios_padroes.mean()

                lsc_xr = media_global + A3 * dp_global
                lic_xr = media_global - A3 * dp_global
                
                # Calculando as métricas
                acima_lsc = sum(medias_linhas > lsc_xr)
                abaixo_lic = sum(medias_linhas < lic_xr)

                # Criando os cartões acima do gráfico
                coluna_esquerda, coluna_direita = st.columns([1, 1])
                coluna_esquerda.metric("Amostras acima do LSC", f'{acima_lsc}')
                coluna_direita.metric("Amostras abaixo do LIC", f'{abaixo_lic}')

                # Criando o gráfico de controle
                fig = go.Figure()
                fig.add_trace(go.Scatter(x=list(range(len(medias_linhas))), y=medias_linhas, mode='lines+markers', name='Média das Amostras'))
                fig.add_trace(go.Scatter(x=list(range(len(desvios_padroes))), y=desvios_padroes, mode='lines+markers', name='Desvio Padrão das Amostras'))
                fig.add_hline(y=lsc_xr, line_color='red', line_dash='dash', annotation_text="LSC", annotation_position="bottom right")
                fig.add_hline(y=lic_xr, line_color='red', line_dash='dash', annotation_text="LIC", annotation_position="bottom right")
                fig.update_layout(title="Gráfico de Controle - Média Vs Desvio Padrão", xaxis_title="Amostras", yaxis_title="Valores")
                st.plotly_chart(fig)

            calcular_media_dp(df)

        elif option == "Média Vs CUSUM":
            # Código para o gráfico de Média Vs CUSUM
            def calcular_cusum(df):
                st.subheader("Gráfico de Controle - Média Vs CUSUM")
                n = df.shape[1]
                fatores = get_factors(n)
                B4 = fatores['B4']

                medias_linhas = df.mean(axis=1)

                # Cálculo do CUSUM
                cusum = np.zeros_like(medias_linhas)
                for i in range(1, len(medias_linhas)):
                    cusum[i] = max(0, cusum[i-1] + medias_linhas[i] - medias_linhas.mean())
                
                # Criando o gráfico de controle
                fig = go.Figure()
                fig.add_trace(go.Scatter(x=list(range(len(medias_linhas))), y=medias_linhas, mode='lines+markers', name='Média das Amostras'))
                fig.add_trace(go.Scatter(x=list(range(len(cusum))), y=cusum, mode='lines+markers', name='CUSUM'))
                fig.add_hline(y=B4, line_color='red', line_dash='dash', annotation_text="Limite de Controle Superior (B4)", annotation_position="bottom right")
                fig.update_layout(title="Gráfico de Controle - Média Vs CUSUM", xaxis_title="Amostras", yaxis_title="Valores")
                st.plotly_chart(fig)

            calcular_cusum(df)
```


 
