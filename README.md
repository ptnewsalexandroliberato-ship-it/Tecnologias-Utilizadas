# Tecnologias-Utilizadas
Orquestração de Workflows Automatizados com AWS Step Functions ☁️  ## 🎯 Objetivo Este projeto foi desenvolvido como parte do desafio prático da **Digital Innovation One (DIO)**. O objetivo é demonstrar a aplicação de workflows automatizados utilizando o **AWS Step Functions** para orquestrar serviços como AWS Lambda, S3 e DynamoDB.

# Orquestração de Workflows Automatizados com AWS Step Functions ☁️

## 🎯 Objetivo
Este projeto foi desenvolvido como parte do desafio prático da **Digital Innovation One (DIO)**. O objetivo é demonstrar a aplicação de workflows automatizados utilizando o **AWS Step Functions** para orquestrar serviços como AWS Lambda, S3 e DynamoDB.

## 🛠️ Tecnologias Utilizadas
*   **AWS Step Functions:** Orquestração visual de fluxos de trabalho.
*   **AWS Lambda:** Execução de lógica de backend (Serverless).
*   **Amazon DynamoDB:** Banco de dados NoSQL para persistência de estados.
*   **JSON / ASL (Amazon States Language):** Definição da máquina de estados.

## 🏗️ Cenário do Workflow
Para este laboratório, foi simulado um fluxo de processamento de dados que executa as seguintes etapas:
1.  **Início:** Recebimento de um payload JSON.
2.  **Validação:** Uma função Lambda verifica a integridade dos dados.
3.  **Processamento:** Se válidos, os dados são salvos no DynamoDB.
4.  **Notificação:** Envio de confirmação via SNS (opcional).
5.  **Tratamento de Erros:** Caso a validação falhe, o fluxo é direcionado para um estado de erro.

## 📊 Visualização do Workflow
> [!TIP]
> No AWS Step Functions, a visualização gráfica permite identificar gargalos e falhas de lógica rapidamente.

### Exemplo de Código ASL (Amazon States Language)
```json
{
  "Comment": "Um exemplo de workflow para validar e salvar dados",
  "StartAt": "ValidaDados",
  "States": {
    "ValidaDados": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:region:account:function:ValidaFn",
      "Next": "DadosValidos?"
    },
    "DadosValidos?": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.isValid",
          "BooleanEquals": true,
          "Next": "SalvaNoBanco"
        }
      ],
      "Default": "NotificaErro"
    },
    "SalvaNoBanco": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:region:account:function:SaveToDynamo",
      "End": true
    },
    "NotificaErro": {
      "Type": "Fail",
      "Error": "ValidationError",
      "Cause": "Os dados fornecidos são inválidos."
    }
  }
}
