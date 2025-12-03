# Relatorio do Projeto AWS Academy
## Aplicativo Web Dimensionavel e Altamente Disponivel

---

## 1. Introducao

### 1.1 Objetivo do Projeto
Este projeto consiste em uma Prova de Conceito (POC) para hospedar o aplicativo web de registros de alunos da Universidade Exemplo na nuvem AWS. O objetivo principal e demonstrar a implementacao de uma arquitetura que atenda aos requisitos de alta disponibilidade, escalabilidade, seguranca e otimizacao de custos.

### 1.2 Escopo
O sistema desenvolvido permite o gerenciamento de registros de alunos atraves de uma interface web, com operacoes de criacao, leitura, atualizacao e exclusao (CRUD) de dados armazenados em banco de dados relacional.

---

## 2. Requisitos Atendidos

| Requisito | Solucao Implementada |
|-----------|---------------------|
| Funcional | Aplicacao Node.js com CRUD completo |
| Carga Balanceada | Application Load Balancer (ALB) |
| Dimensionavel | Auto Scaling Group |
| Alta Disponibilidade | Multi-AZ com ALB |
| Seguro | VPC, Security Groups, Secrets Manager |
| Economico | Instancias t3.micro, RDS db.t3.micro |
| Alto Desempenho | Auto Scaling baseado em CPU |

---

## 3. Arquitetura da Solucao

### 3.1 Visao Geral
A arquitetura foi projetada seguindo as melhores praticas do AWS Well-Architected Framework, com foco em:
- Isolamento de rede atraves de VPC
- Separacao de camadas publicas e privadas
- Redundancia em multiplas zonas de disponibilidade
- Escalabilidade automatica baseada em demanda

### 3.2 Componentes da Arquitetura

#### 3.2.1 VPC (Virtual Private Cloud)
- CIDR Block: 10.0.0.0/16
- Regiao: us-east-1
- Funcao: Isolar a infraestrutura em uma rede privada virtual

#### 3.2.2 Subnets
- Public-Subnet-A (10.0.1.0/24) - us-east-1a
- Public-Subnet-B (10.0.2.0/24) - us-east-1b
- Private-Subnet-A (10.0.3.0/24) - us-east-1a
- Private-Subnet-B (10.0.4.0/24) - us-east-1b

As subnets publicas hospedam os servidores web com acesso a internet.
As subnets privadas hospedam o banco de dados, sem acesso direto a internet.

#### 3.2.3 Internet Gateway
Permite a comunicacao entre a VPC e a internet publica.

#### 3.2.4 Application Load Balancer (ALB)
- Tipo: Internet-facing
- Protocolo: HTTP na porta 80
- Funcao: Distribuir o trafego entre as instancias EC2 e realizar health checks

#### 3.2.5 EC2 (Elastic Compute Cloud)
- Tipo de instancia: t3.micro
- Sistema operacional: Ubuntu Server 24.04 LTS
- Aplicacao: Node.js com Express.js
- Porta: 80

#### 3.2.6 Auto Scaling Group
- Capacidade minima: 2 instancias
- Capacidade maxima: 6 instancias
- Capacidade desejada: 2 instancias
- Politica de escalabilidade: Target Tracking baseado em CPU (50%)

#### 3.2.7 Amazon RDS (Relational Database Service)
- Engine: MySQL 8.0
- Tipo de instancia: db.t3.micro
- Armazenamento: 20 GB SSD (gp2)
- Configuracao: Single-AZ
- Acesso publico: Desabilitado

#### 3.2.8 AWS Secrets Manager
Armazena as credenciais do banco de dados de forma segura, permitindo que a aplicacao recupere as informacoes em tempo de execucao.

#### 3.2.9 IAM (Identity and Access Management)
- LabRole: Permissoes para recursos AWS
- LabInstanceProfile: Permite que instancias EC2 acessem outros servicos AWS

---

## 4. Fluxo de Dados

1. Usuario acessa a aplicacao atraves da URL do ALB
2. ALB recebe a requisicao e direciona para uma instancia EC2 saudavel
3. Instancia EC2 executa a aplicacao Node.js
4. Aplicacao consulta o Secrets Manager para obter credenciais do banco
5. Aplicacao conecta ao RDS MySQL e executa as operacoes
6. Resposta e retornada ao usuario atraves do ALB

---

## 5. Seguranca

### 5.1 Security Groups Implementados

#### ALB-SG (Load Balancer)
- Entrada: HTTP (80) de qualquer origem (0.0.0.0/0)

#### WebServer-SG (Servidores Web)
- Entrada: HTTP (80) do ALB-SG
- Entrada: SSH (22) do IP do administrador
- Saida: Todo trafego permitido

#### RDS-SG (Banco de Dados)
- Entrada: MySQL (3306) apenas do WebServer-SG

### 5.2 Principios de Seguranca Aplicados
- Principio do menor privilegio nos Security Groups
- Banco de dados em subnet privada sem acesso a internet
- Credenciais armazenadas no Secrets Manager
- Comunicacao interna via rede privada

---

## 6. Estimativa de Custos

### 6.1 Resumo Mensal (Regiao us-east-1)

| Servico | Especificacao | Custo Mensal (USD) |
|---------|---------------|-------------------|
| EC2 (Web Servers) | 2x t3.micro (730h/mes cada) | 16.94 |
| EC2 (Cloud9) | 1x t3.micro (100h/mes) | 1.16 |
| Application Load Balancer | 1 ALB + 10 LCU | 22.27 |
| Amazon RDS MySQL | db.t3.micro, 20GB, Single-AZ | 15.73 |
| AWS Secrets Manager | 1 segredo | 0.40 |
| Data Transfer | 10GB/mes outbound | 0.90 |
| EBS | 3x 8GB gp3 | 2.40 |
| Total Mensal | | 59.80 |
| Total Anual | | 717.60 |

### 6.2 Detalhamento

#### Amazon EC2
- Preco On-Demand t3.micro: $0.0116/hora
- 2 instancias x 730 horas x $0.0116 = $16.94/mes

#### Application Load Balancer
- Taxa fixa: $0.0225/hora x 730 = $16.43/mes
- LCU: aproximadamente $5.84/mes

#### Amazon RDS
- Instancia db.t3.micro: $0.017/hora x 730 = $12.41/mes
- Armazenamento: 20GB x $0.115 = $2.30/mes
- Backup: Incluido (7 dias)

---

## 7. Fases do Projeto

### 7.1 Fase 1 - Planejamento
- Elaboracao do diagrama de arquitetura
- Calculo da estimativa de custos
- Definicao dos requisitos tecnicos

### 7.2 Fase 2 - Aplicativo Web Basico (POC)
- Criacao da VPC e componentes de rede
- Lancamento de instancia EC2 com aplicacao
- Banco de dados local para validacao inicial

### 7.3 Fase 3 - Desacoplamento
- Criacao de subnets adicionais (publicas e privadas)
- Provisionamento do Amazon RDS
- Configuracao do AWS Secrets Manager
- Migracao dos dados para RDS
- Nova instancia EC2 conectando ao RDS

### 7.4 Fase 4 - Alta Disponibilidade
- Criacao do Target Group
- Configuracao do Application Load Balancer
- Criacao da AMI da instancia web
- Configuracao do Launch Template
- Criacao do Auto Scaling Group
- Testes de carga e validacao

---

## 8. Tecnologias Utilizadas

### 8.1 Backend
- Node.js 20.x LTS
- Express.js 4.21
- mysql2 3.11
- AWS SDK para JavaScript v3

### 8.2 Banco de Dados
- MySQL 8.0

### 8.3 Infraestrutura AWS
- VPC, Subnets, Internet Gateway
- EC2, EBS
- Application Load Balancer
- Auto Scaling Group
- Amazon RDS
- AWS Secrets Manager
- IAM
- AWS Cloud9

---

## 9. Funcionalidades da Aplicacao

### 9.1 Interface Web
- Listagem de alunos cadastrados
- Formulario para adicao de novos alunos
- Botao para exclusao de registros
- Exibicao de informacoes do servidor

### 9.2 API REST

| Metodo | Rota | Descricao |
|--------|------|-----------|
| GET | / | Pagina principal com interface web |
| GET | /api/students | Lista todos os alunos |
| POST | /api/students | Adiciona novo aluno |
| PUT | /api/students/:id | Atualiza dados do aluno |
| DELETE | /api/students/:id | Remove aluno |
| GET | /health | Health check para o ALB |

### 9.3 Modelo de Dados

Tabela: students
- id: INT (Auto Increment, Primary Key)
- name: VARCHAR(100) NOT NULL
- email: VARCHAR(100) NOT NULL
- course: VARCHAR(100)
- year: INT
- created_at: TIMESTAMP

---

## 10. Conclusao

O projeto foi desenvolvido com sucesso, atendendo a todos os requisitos estabelecidos:

1. Funcionalidade: Sistema CRUD completo e funcional
2. Balanceamento de Carga: ALB distribuindo trafego entre instancias
3. Escalabilidade: Auto Scaling configurado para responder a demanda
4. Alta Disponibilidade: Arquitetura Multi-AZ com redundancia
5. Seguranca: Multiplas camadas de protecao implementadas
6. Economia: Utilizacao de instancias otimizadas para custo
7. Desempenho: Resposta rapida mesmo sob carga

A arquitetura implementada segue as melhores praticas da AWS e esta preparada para um ambiente de producao, podendo ser facilmente expandida conforme as necessidades do negocio.

---

## 11. Referencias

- AWS Well-Architected Framework
- AWS Pricing Calculator
- Amazon EC2 Documentation
- Amazon RDS User Guide
- Application Load Balancer Guide
- AWS Secrets Manager Documentation

---

Data: Dezembro de 2025
Versao: 1.0
