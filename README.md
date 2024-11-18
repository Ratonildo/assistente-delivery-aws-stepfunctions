# assistente-delivery-aws-stepfunctions
Assistente Delivery para sugestionar ao usuário acompanhamentos e lugares para uma experiência gastronômica romântica
{
  "Comment": "Assistente de Delivery com sugestões de acompanhamentos, bebidas e local para experiência romântica",
  "StartAt": "EscolherPratoPrincipal",
  "States": {
    "EscolherPratoPrincipal": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invoke",
      "Parameters": {
        "ModelId": "haiku",
        "Input": {
          "Instruction": "O usuário escolheu um prato principal. Sugira acompanhamentos que combinem com o prato.",
          "UserInput": "$.PratoPrincipal"
        }
      },
      "ResultPath": "$.SugestoesAcompanhamentos",
      "Next": "SugestaoDeBebidas"
    },
    "SugestaoDeBebidas": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invoke",
      "Parameters": {
        "ModelId": "haiku",
        "Input": {
          "Instruction": "Baseado no prato principal e nos acompanhamentos, sugira bebidas que harmonizem.",
          "UserInput": {
            "PratoPrincipal": "$.PratoPrincipal",
            "Acompanhamentos": "$.SugestoesAcompanhamentos"
          }
        }
      },
      "ResultPath": "$.SugestoesBebidas",
      "Next": "SugestaoDeLugar"
    },
    "SugestaoDeLugar": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invoke",
      "Parameters": {
        "ModelId": "haiku",
        "Input": {
          "Instruction": "Baseado na comida e bebida sugeridas, sugira um lugar que proporcione uma experiência romântica.",
          "UserInput": {
            "PratoPrincipal": "$.PratoPrincipal",
            "Acompanhamentos": "$.SugestoesAcompanhamentos",
            "Bebidas": "$.SugestoesBebidas"
          }
        }
      },
      "ResultPath": "$.SugestaoLugar",
      "Next": "Finalizar"
    },
    "Finalizar": {
      "Type": "Succeed"
    }
  }
}


Explicação do Fluxo:

EscolherPratoPrincipal: O assistente inicia com a entrada do prato principal e utiliza o modelo HAIKU para sugerir acompanhamentos.
SugestaoDeBebidas: Com base no prato e acompanhamentos, sugere bebidas apropriadas.
SugestaoDeLugar: Com todos os itens definidos, sugere um local que proporcione uma experiência romântica.
Finalizar: O fluxo é concluído.

Observações:

ModelId: O identificador do modelo HAIKU do Amazon Bedrock.
Parameters: Cada tarefa define instruções e entradas específicas para gerar as sugestões. Os campos Input podem ser ajustados conforme necessário.
ResultPath: As respostas do modelo são armazenadas em caminhos específicos no estado para serem usadas nas próximas etapas.
