# Previsão de Churn em Telecomunicações

Trabalho de Machine Learning, Unidade 3.
Integrantes: Karoline Costa e Gabriel Sad

## 1. Problema
Classificação binária: prever se um cliente vai cancelar o serviço (*churn*). Reter clientes costuma custar menos que conquistar novos, e a previsão permite agir antes do cancelamento.

## 2. Dados
Base pública **Telco Customer Churn** (Kaggle), em CSV. Cada linha é um cliente, com dados demográficos, serviços contratados, tipo de contrato, forma de pagamento, tempo de casa (`tenure`), valores cobrados e o alvo `Churn`.

## 3. Pré-processamento
- Conversão de `TotalCharges` para número; total 0 para clientes com `tenure = 0` sem valor.
- Remoção de duplicatas e de `customerID`.
- Numéricas: imputação pela mediana e padronização.
- Categóricas: imputação pela moda e one-hot encoding.
- Transformações dentro de um `Pipeline`, ajustadas só no treino (sem vazamento).

## 4. Divisão dos dados
Estratificada, semente 42: 60% treino, 20% validação, 20% teste.

## 5. Modelagem
Regressão Logística, Random Forest e SVM, com `GridSearchCV` (5 dobras estratificadas, métrica F1). Um classificador trivial serve de referência. O limiar de decisão e o modelo final são escolhidos na validação.

## 6. Avaliação
Acurácia, precisão, recall, F1, ROC-AUC e PR-AUC no teste, com matrizes de confusão, curvas ROC e precisão-recall e importância por permutação. A acurácia sozinha engana quando as classes são desbalanceadas, por isso priorizamos recall e F1.

## 7. Resultados e discussão
Conjunto de teste (1.409 clientes, 374 cancelamentos), com limiar ajustado na validação:

| Modelo | Acurácia | Precisão | Recall | F1 | ROC-AUC | PR-AUC |
|---|---|---|---|---|---|---|
| Referência (sempre "Não") | 0,735 | 0,000 | 0,000 | 0,000 | 0,500 | 0,265 |
| Regressão Logística | 0,779 | 0,578 | 0,626 | 0,601 | 0,830 | 0,621 |
| **Random Forest (escolhido)** | 0,749 | 0,518 | 0,789 | 0,625 | 0,830 | 0,629 |
| SVM | 0,772 | 0,561 | 0,650 | 0,602 | 0,820 | 0,586 |

- O Random Forest identificou 295 dos 374 cancelamentos (79 não detectados), com 275 alarmes falsos.
- Os três modelos têm desempenho muito próximo. No limiar padrão, a Regressão Logística teve F1 0,623 e recall 0,805, praticamente empatada com o Random Forest.
- O ajuste de limiar feito na validação não melhorou o F1 no teste da Regressão Logística (0,623 para 0,601) nem do SVM (0,618 para 0,602).
- A referência trivial tem 73,5% de acurácia sem detectar nenhum cancelamento, o que mostra por que a acurácia não basta.
- Variáveis mais importantes (permutação): `Contract` (de longe), `InternetService`, `TotalCharges`, `tenure` e `TechSupport`. Importância não implica causalidade.

## 8. Como reproduzir
**Google Colab**
1. Abra `churn_telco.ipynb`.
2. Use *Ambiente de execução > Executar tudo*.
3. Quando solicitado, envie o ZIP ou CSV da base.

**Localmente**
```bash
pip install -r requirements.txt
# o ZIP ou CSV da base deve estar nesta pasta
jupyter notebook churn_telco.ipynb
```

## 9. Estrutura
```
churn_telco.ipynb   código completo
requirements.txt    dependências
figuras/            gráficos (gerados na execução)
resultados/         tabelas em CSV (geradas na execução)
```
