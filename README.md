# Tendência e Ciclo da Série Temporal

## 📊 Objetivo
Decomposição de Tendência e Ciclo utilizando MQO.


## Passo a Passo da Análise

### 1. Importação e tratamento dos dados
O arquivo excel importado está carregado no repositório.
```r
file.choose()
rota_dados <- "D:\\.MEU\\FECAP\\Modelagem Preditiva\\Macroeconometria\\Exemplos\\Atividade 2 - Ciclo\\Base\\Macroeconometria - Ciclo.xls"
dados_brutos <- read_excel(rota_dados)
dados_brutos

dados_tend <- dados_brutos |> 
  select(ano = ...1,
         pib_ca = Yca,
         pib_ho = Yho) |> 
  mutate(time = row_number(),
         time2 = time * time,
         time3 = time * time * time)
```

### 2. Instalando as fontes de texto utilizadas
```r
font_add_google("Poppins", "poppins")
showtext_auto()

font_add_google("Dosis", "dosis")
showtext_auto()
```

### 3. Tabela descritiva
Tabela contendo valor mínimo, valor máximo, média, mediana, 1Q, 3Q e AIQ.
```r
summary(dados_tend)
```

### 4. Modelo de Regressão - MQO
- **Variável Dependente:** pib_ca - PIB do Canadá
- **Variáveis Independentes:** 
  - time - Tendência temporal
  - time2 - Tendência temporal ao quadrado
```r
modelo <- lm(pib_ca ~ time + time2, data = dados_tend)
summary(modelo)
```
(sumario.png)

### 5. Extraindo o componente de tendência e ciclo
- **Valores Ajustados:** tendência da série temporal
- **Resíduos:** ciclo da série temporal
```r
# Salvando os Valores Ajustados para Tendência
valores_ajustados <- modelo |>
  fitted.values()

# Salvando os Rescíduos para Ciclo
residuos <- modelo |> 
  residuals()
```

### 5. Visualização Gráfica
Gráfico de Tendência:
```r
ggplot(dados_tend, aes(x = ano)) +
  geom_line(aes(y = valores_ajustados, colour = 'tendência'), 
            color = '#01A77F',
            linewidth = 1.25) +
  geom_line(aes(y = pib_ca), 
            color = '#CF3011',
            linewidth = 1.25) +
  labs(title = 'Tendência do PIB do Canadá',
       subtitle = 'Tendência X Original',
       x = NULL,
       y = 'Valores',
       caption = 'Elaboração: Caio Nunes') + 
  theme(panel.background = element_rect(fill = 'white'),
        plot.title = element_text(family = 'dosis', hjust = 0.5, vjust = -1,
                                  face = 'bold', color = '#01A77F', size = 19),
        plot.subtitle = element_text(family = 'poppins', hjust = 0.5, vjust = -1, 
                                     size = 11),
        plot.caption = element_text(family = 'poppins', hjust = 1, vjust = 1, color = 'black', size = 9),
        legend.position = 'bottom',
        legend.text = element_text(family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        legend.title = element_text(face = 'bold', family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        axis.title = element_text(family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        axis.text = element_text(family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        axis.line.x = element_line(),
        axis.line.y = element_line())
```
![Gráfico Série Original X Tendência](x.png)


Gráfico de Ciclo:
```r
ggplot(dados_tend, aes(x = ano)) +
  geom_line(aes(y = residuos, colour = 'Ciclo'), 
            color = 'green',
            linewidth = 1.25) +
  geom_hline(yintercept = 0,
             linetype = 'dashed',
             alpha = 0.5) +
  labs(title = 'Ciclo do PIB do Canadá',
       subtitle = NULL,
       x = NULL,
       y = 'Valores',
       caption = 'Elaboração: Caio Nunes') + 
  theme(panel.background = element_rect(fill = 'white'),
        plot.title = element_text(family = 'dosis', hjust = 0.5, vjust = -1,
                                  face = 'bold', color = 'black', size = 19),
        plot.subtitle = element_text(family = 'poppins', hjust = 0.5, vjust = -1, 
                                     size = 11),
        plot.caption = element_text(family = 'poppins', hjust = 1, vjust = 1, color = 'black', size = 9),
        legend.position = 'bottom',
        legend.text = element_text(family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        legend.title = element_text(face = 'bold', family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        axis.title = element_text(family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        axis.text = element_text(family = 'poppins', hjust = 0.5, color = 'black', size = 9),
        axis.line.x = element_line(),
        axis.line.y = element_line())
```
![Gráfico do Ciclo](y.png)
