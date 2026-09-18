# Resumo de Estudos: Arquitetura Serverless, Containers e Mensageria na AWS ☁️

Este repositório guarda minhas anotações e resumos práticos do bootcamp da DIO. O foco aqui é entender como a AWS gerencia código, containers, mensagens e como amarrar tudo isso em um fluxo de trabalho organizado. Escrevi de forma simples e direta para servir como material de apoio (e para salvar minha vida em consultas futuras!).

---

## ⚡ AWS Lambda (Computação Serverless)

O Lambda é o coração do modelo *Serverless* (sem servidor) na AWS. Na prática, você não precisa se preocupar em alocar uma máquina, instalar sistema operacional ou gerenciar memória para rodar um script.

* **Como funciona:** Você sobe o seu código (uma função), escolhe o gatilho (um arquivo salvo, um clique no site, um horário específico) e a AWS roda o código para você.
* **Vantagem de estudante:** Você só paga pelo tempo exato (em milissegundos) que o código estiver rodando. Se ninguém usar, a conta é zero. É perfeito para tarefas rápidas e automatizações.

  ⚡ AWS Lambda (Automação Rápida e Invisível) Exemplo:

    Exemplo Prático: Você cria um site onde o usuário faz upload de uma foto de perfil. Sempre que a foto é salva, um gatilho aciona uma função Lambda. Ela roda por 200 milissegundos, corta a imagem, cria uma versão em miniatura, salva no banco e desliga. Você só paga por esses 200 milissegundos.

    No Mundo Real: A Netflix utiliza funções Serverless massivamente nos bastidores. Quando um estúdio faz o upload de um novo episódio de série, funções Lambda são disparadas automaticamente para verificar o arquivo e iniciar o processo de codificação do vídeo para diferentes resoluções e dispositivos, sem que um servidor fique ligado 24/7 esperando arquivos chegarem.

  <img width="575" height="306" alt="aws lambda" src="https://github.com/user-attachments/assets/237596ab-cb3d-43f0-b64e-34a602a7f79d" />


## 🐳 Orquestração de Containers: Amazon ECS e EKS

Quando o projeto cresce, colocar a aplicação dentro de "Containers" (como o Docker) garante que o código vai rodar igual em qualquer lugar. Mas gerenciar dezenas de containers na mão é caótico. É aí que entram os orquestradores.

* **Amazon ECS (Elastic Container Service):** É a solução nativa da AWS. É mais simples, fácil de configurar e se integra perfeitamente com o resto da nuvem da Amazon. Ótimo para quem quer subir containers rápido sem muita dor de cabeça.
* **Amazon EKS (Elastic Kubernetes Service):** É o peso-pesado. Ele usa o **Kubernetes**, que é o padrão da indústria hoje. É mais complexo de configurar que o ECS, mas oferece um controle absurdo e flexibilidade. Se você aprender EKS, está aprendendo uma ferramenta que pode ser usada fora da AWS também.

  🐳 Amazon ECS e EKS (A Base dos Apps Modernos) Exemplos:

    Exemplo Prático: Uma loja virtual opera com tráfego normal o ano todo. Na Black Friday, os acessos multiplicam por 100. O EKS percebe o pico de acessos, lê as métricas de CPU e cria automaticamente dezenas de novos containers (cópias idênticas do sistema da loja) para aguentar a carga. Quando a promoção acaba, ele destrói os containers excedentes para economizar recursos.

    No Mundo Real: O Nubank e o Uber rodam arquiteturas baseadas em microsserviços usando orquestração pesada (como Kubernetes no EKS). Isso permite que eles atualizem o serviço do PIX ou a lógica do GPS sem precisar desligar o resto do aplicativo, já que cada função do app vive em seu próprio container e todos são gerenciados de forma automatizada pelo orquestrador.

  <img width="563" height="299" alt="EKS" src="https://github.com/user-attachments/assets/04382e82-efba-4452-919a-f33b4d1dc47d" />


## 📬 Comunicação Assíncrona: Amazon SNS e SQS

Quando temos vários serviços (microsserviços), eles precisam conversar entre si. Fazer eles conversarem de forma *assíncrona* significa que um serviço não precisa ficar esperando o outro terminar para continuar trabalhando.

* **Amazon SNS (Simple Notification Service - O "Megafone"):** Funciona no modelo *Pub/Sub* (Publicador/Inscrito). Um serviço grita uma mensagem ("Novo pedido feito!") e vários outros serviços que estão escutando recebem essa mensagem ao mesmo tempo.
* **Amazon SQS (Simple Queue Service - A "Fila do Banco"):** É literalmente uma fila de mensagens. Se você tem milhares de pedidos chegando e seu banco de dados é lento, você joga os pedidos no SQS. A aplicação vai pegando um por um da fila no ritmo dela, sem travar e sem perder nenhuma informação.

> **Dica de Ouro:** É muito comum usar os dois juntos. O SNS avisa que algo aconteceu e joga a mensagem em várias filas SQS diferentes para cada setor processar no seu tempo.

📬 Comunicação Assíncrona: Amazon SNS e SQS Exemplo:

Quando temos vários serviços (microsserviços), eles precisam conversar entre si. Fazer eles conversarem de forma assíncrona significa que um serviço não precisa ficar esperando o outro terminar para continuar trabalhando.

    Amazon SNS (Simple Notification Service - O "Megafone"): Funciona no modelo Pub/Sub (Publicador/Inscrito). Um serviço grita uma mensagem ("Novo pedido feito!") e vários outros serviços que estão escutando recebem essa mensagem ao mesmo tempo.

    Amazon SQS (Simple Queue Service - A "Fila do Banco"): É literalmente uma fila de mensagens. Se você tem milhares de pedidos chegando e seu banco de dados é lento, você joga os pedidos no SQS. A aplicação vai pegando um por um da fila no ritmo dela, sem travar e sem perder nenhuma informação.

    Dica de Ouro: É muito comum usar os dois juntos. O SNS avisa que algo aconteceu e joga a mensagem em várias filas SQS diferentes para cada setor processar no seu tempo.


  <img width="570" height="306" alt="SNS" src="https://github.com/user-attachments/assets/8746be8d-bafb-4b98-a8c8-6bfb2db15bb2" />


## 🗺️ AWS Step Functions (O Maestro)

Se você tem uma arquitetura cheia de funções Lambda, filas SQS e containers ECS rodando, como você acompanha o processo inteiro? O Step Functions resolve isso.

* **O que é:** É um serviço que permite construir *workflows* (fluxos de trabalho) visuais.
* **Na prática:** Você cria uma "máquina de estados". Por exemplo: o fluxo começa no Passo A (uma Lambda que valida o cartão). Se der certo, vai para o Passo B (manda pro SQS para faturar). Se der erro, vai para o Passo C (manda um email pelo SNS avisando o cliente).
* Você vê todo o diagrama visualmente na AWS, sabe exatamente onde o processo travou se der erro, e consegue coordenar aplicações gigantescas de forma previsível e organizada.

  🗺️ AWS Step Functions (O Maestro) Exemplo:

Se você tem uma arquitetura cheia de funções Lambda, filas SQS e containers ECS rodando, como você acompanha o processo inteiro? O Step Functions resolve isso.

    O que é: É um serviço que permite construir workflows (fluxos de trabalho) visuais.

     Na prática: Você cria uma "máquina de estados". Por exemplo: o fluxo começa no Passo A (uma Lambda que valida o cartão). Se der certo, vai para o Passo B (manda pro SQS para faturar). Se der erro, vai para o Passo C (manda um email pelo SNS avisando o cliente).

    Você vê todo o diagrama visualmente na AWS, sabe exatamente onde o processo travou se der erro, e consegue coordenar aplicações gigantescas de forma previsível e organizada.

    
  <img width="568" height="306" alt="stepfun" src="https://github.com/user-attachments/assets/bcf04746-b489-434f-bf02-3cf2c77b6092" />

