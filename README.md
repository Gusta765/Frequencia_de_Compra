# 📊 Análise de Cesta de Compras (Market Basket Analysis)

## 1. Resumo do Projeto

Este projeto realiza uma **Análise de Cesta de Compras** para descobrir padrões de consumo e associações entre produtos vendidos. O objetivo principal é identificar quais produtos são frequentemente comprados juntos para, a partir daí, gerar insights acionáveis que possam impulsionar as vendas e melhorar a experiência do cliente.

A análise utiliza a métrica de **Confiança (Confidence)** para determinar a probabilidade de um cliente comprar um produto B, dado que ele já comprou o produto A.

**Tecnologias utilizadas:** Python, Pandas.

---

## 2. Como a Análise Funciona

O script segue três etapas fundamentais para calcular as regras de associação:

### Etapa 1: Frequência de Pares de Produtos

Primeiro, identificamos todos os pares de produtos que foram comprados juntos em um mesmo pedido e contamos a frequência de cada par.

```python
# Gerar pares de produtos por pedido
pares = (
    df.merge(df, on="pedido_id")
      .query("produto_id_x < produto_id_y")
      .groupby(["produto_id_x", "produto_id_y"])
      .size()
      .reset_index(name="qtd_juntos")
)
```

### Etapa 2: Frequência de Produtos Individuais (Suporte do Item)

Em seguida, calculamos a frequência total de vendas de cada produto individualmente. Este valor é crucial, pois serve como base para o cálculo da confiança.

```python
# Contar quantas vezes cada produto apareceu em pedidos
total_a = (
    df.groupby("produto_id")
      .size()
      .reset_index(name="qtd_a")
      .rename(columns={"produto_id": "produto_a"})
)
```

### Etapa 3: Cálculo da Confiança (Confidence)

A confiança da regra "A → B" nos diz: "Daqueles que compraram o produto A, qual a porcentagem que também comprou o produto B?". Uma confiança alta indica uma forte associação entre os produtos.

> **Fórmula:** `Confiança(A → B) = (Nº de transações com A e B) / (Nº de transações com A)`

```python
# Renomeando colunas para clareza
pares = pares.rename(columns={"produto_id_x": "produto_a", "produto_id_y": "produto_b"})

# Calculando a confiança e ordenando os resultados
analise_confianca = (
    pares.merge(total_a, on="produto_a")
         .assign(confianca=lambda x: round(x["qtd_juntos"] / x["qtd_a"], 2))
         .sort_values("confianca", ascending=False)
         .reset_index(drop=True)
)

# Exibindo as 10 regras com maior confiança
display(analise_confianca.head(10))
```

---

## 🚀 3. Como Gerar Impacto nas Vendas com esta Análise

Descobrir que "70% dos clientes que compram o produto A também compram o produto B" é um insight poderoso. Aqui estão algumas estratégias de negócio que podem ser implementadas a partir desses resultados para aumentar as vendas:

#### 🛒 **Otimização do Layout da Loja (Física e Digital)**
* **Loja Física:** Posicione produtos frequentemente comprados juntos em locais próximos nas prateleiras para incentivar a compra por impulso e facilitar a jornada do cliente.
* **E-commerce:** Na página do produto A, crie uma seção "Comprados Juntos Frequentemente" exibindo o produto B. Isso aumenta drasticamente a chance de cross-sell.

#### 💡 **Marketing e Promoções Direcionadas (Cross-selling)**
* **Criação de Combos:** Ofereça pacotes promocionais. "Compre o Produto A + Produto B e ganhe 15% de desconto!".
* **E-mail Marketing e Notificações:** Envie um e-mail para um cliente que comprou o produto A na semana passada. "Vimos que você gostou do Produto A. Que tal experimentar o Produto B, o par perfeito para ele?".

#### 🤖 **Sistemas de Recomendação**
* As regras de associação geradas por esta análise são a base para sistemas de recomendação mais simples e eficazes. A lógica "se o cliente adicionou A ao carrinho, recomende B" pode ser implementada diretamente no site.

#### 📦 **Gestão de Estoque e Logística**
* Garanta que produtos com alta associação de compra estejam sempre em estoque e, se possível, armazenados próximos no depósito para otimizar o processo de separação de pedidos (picking). A falta de um item pode impedir a venda de ambos.

---

## 4. Como Utilizar este Projeto

1.  **Pré-requisitos:** Certifique-se de ter Python e a biblioteca Pandas instalada (`pip install pandas`).
2.  **Dados:** Coloque seu arquivo de vendas no mesmo diretório do script e nomeie-o como `base_vendas_produtos.csv`. A base deve conter, no mínimo, as colunas `pedido_id` e `produto_id`.
3.  **Execução:** Rode o script Python ou as células do notebook para obter o DataFrame final com a análise de confiança.

---

## 5. Próximos Passos e Melhorias

Para aprofundar a análise, os próximos passos incluem:
* **Calcular o Lift:** Medir se o par de produtos aparece junto com mais frequência do que o esperado pelo acaso. Um Lift > 1 indica uma associação positiva.
* **Filtrar por Suporte Mínimo:** Focar a análise apenas em regras que envolvem produtos com um volume mínimo de vendas para garantir relevância estatística.
* **Visualização de Dados:** Criar um mapa de calor ou um gráfico de rede para visualizar as relações mais fortes entre os produtos.

🧑‍💻 Autor

Desenvolvido por Gustavo Barbosa

### 📫 Contato

[![LinkedIn](https://img.shields.io/badge/-LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/gustavo-barbosa-868976236/) [![Email](https://img.shields.io/badge/Email-gustavobarbosa7744@gmail.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:gustavobarbosa7744@gmail.com)
