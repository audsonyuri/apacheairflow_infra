# Apache Airflow - Local and Cloud Deployment

## 🚀 Overview

Este repositório fornece uma solução completa para implantação do **Apache Airflow** em dois ambientes:
1. **Ambiente Local**: Para testes e compreensão de como o Airflow funciona.
2. **Ambiente na Nuvem (AWS)**: Sugestão de uma arquitetura escalável e segura utilizando serviços gerenciados da AWS como **MWAA**, **RDS**, **Elasticache(Redis)** e **S3**.

## 📚 Intenção do Projeto

Este projeto foi criado para demonstrar minhas habilidades e conhecimentos como SRE e para responder a um teste técnico "dre-3-test".
[Exponho aqui](/dev_files/Projeto_Data_Reliability_Engineer_Teste_Tecnico_AudsonYuriFRozendo.pdf) a documentação detalhada em resposta ao desafio.

Aproveito para oferecer uma base sólida para **entusiastas de DevOps** e **profissionais de SRE** aprenderem sobre orquestração de workflows no Airflow em um ambiente local antes de migrar para a nuvem. 

---

## ✨ Frase de Efeito

_"Estude, teste, e colabore! A transformação está no aprendizado e na prática."_  

---

## 📂 Componentes Envolvidos

1. **Ambiente Local**: Configuração do Apache Airflow utilizando Docker e Docker Compose.
2. **Ambiente Cloud (AWS)**: Arquitetura sugerida utilizando **AWS MWAA**, **RDS (Postgres)**, **Elasticache (Redis)** e **S3**.

---

## 🛠️ Ambiente Local - Instalação

### 1. Pré-requisitos

Certifique-se de ter as seguintes ferramentas instaladas:
- **Docker**: Para criar containers localmente.
- **Docker Compose**: Para orquestrar a infraestrutura localmente.

### 2. Configuração do Ambiente Local

Clone este repositório e siga os passos abaixo para configurar o ambiente local:

```bash
git clone https://github.com/audsonyuri/apacheairflow_infra.git
cd apacheairflow_infra
```

### 3. Configuração de Diretórios e Permissões

No host que executa a orquestração do Airflow, é necessário ajustar os direitos de acesso nos diretórios **logs**, **dags** e **plugins**, que são bindados pelo Docker. Isso garante que o Airflow (executando com o usuário UID e GID 50000) tenha permissões plenas de leitura e escrita nesses diretórios.

Estando no diretório raiz do projeto, execute os comandos abaixo:

```bash
mkdir logs dags plugins
sudo chown -R 50000:50000 ./logs ./dags ./plugins
```

Isso garante que o Airflow tenha as permissões adequadas para gerenciar esses diretórios.

### 4. Executando o Airflow Localmente

Com a estrutura configurada, você pode iniciar os serviços do Airflow:

```bash
docker-compose up -d
```

Após a execução, o Airflow estará disponível no navegador em `http://localhost:8080`.

### 5. Acessando o Ambiente

Abra o navegador e acesse o endereço [http://localhost:8080](http://localhost:8080) para interagir com o Airflow local. Este ambiente é ideal para testes e entendimento da execução de DAGs, sem custos de infraestrutura cloud.

### 6. Diagrama de Arquitetura e Fluxo de uso

[Componentes da arquitetura:](/dev_files/airflow-components.png)


[Fluxo comum de uso:](/dev_files/airflow-sequence.png)

---

## 🚦 Health Checks

Para garantir a alta disponibilidade e o monitoramento contínuo dos serviços, health checks foram configurados para os principais componentes.

- **Postgres**:
  ```yaml
  test: ["CMD", "pg_isready", "-U", "airflow"]
  interval: 5s
  retries: 5
  ```

- **Redis**:
  ```yaml
  test: ["CMD", "redis-cli", "ping"]
  interval: 5s
  timeout: 30s
  retries: 50
  ```

- **Airflow Webserver**:
  ```yaml
  test: ["CMD", "curl", "--fail", "http://localhost:8080/health"]
  interval: 10s
  timeout: 10s
  retries: 5
  ```

---

## ☁️ Sugestão de Ambiente Cloud (AWS)

### Visão Geral

Após entender o funcionamento do Airflow em um ambiente local, a migração para um ambiente cloud oferece escalabilidade, alta disponibilidade e integração com serviços gerenciados. Abaixo, sugiro uma arquitetura cloud utilizando **AWS** para produção.


### Componentes:

1. **IAM**: Controle de acesso com políticas específicas para **RDS**, **Elasticache**, **S3** e **MWAA**.
2. **VPC**: Isolamento de rede com subnets públicas e privadas, controladas por **Security Groups**.
3. **RDS (Postgres)**: Banco de dados para armazenamento de metadados, configurado com backups automáticos.
4. **Elasticache (Redis)**: Cache utilizado como broker do CeleryExecutor.
5. **S3**: Buckets para armazenamento de **DAGs**, **Logs**, e **Artefatos**.
6. **AWS MWAA**: Serviço gerenciado de Airflow, escalável e integrado com outros serviços da AWS.

### Arquitetura Sugerida

Abaixo está um diagrama sugerido para a arquitetura na AWS:

![AWS Architecture](/dev_files/AWS_Simple_Architecture.png)

### Descrição dos Componentes

- **Subnets Privadas**: Contêm o **RDS (Postgres)** e o **Elasticache (Redis)**, garantindo comunicação segura entre os componentes.
- **Subnets Públicas**: Contêm o **MWAA** e o **S3** para o armazenamento de arquivos e logs.
- **NAT Gateway**: Permite que os recursos privados acessem a internet sem exposição direta.
- **Security Groups**: Controlam o tráfego entre os serviços internos da AWS e o ambiente externo.


## 🎓 Incentivo ao Estudo

Este projeto oferece uma oportunidade para quem deseja aprender a orquestrar workflows com o **Apache Airflow**, primeiro em um ambiente local e, posteriormente, migrando para a nuvem da AWS. A prática local ajuda a entender o funcionamento do Airflow antes de escalar para um ambiente mais complexo como a AWS.

---

## 🤝 Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir **issues** ou enviar **pull requests**. A colaboração é a chave para o sucesso na comunidade DevOps e SRE.

---

## 📜 Licença

Este projeto está licenciado sob os termos do [MIT License](LICENSE).

---