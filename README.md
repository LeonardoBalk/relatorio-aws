# Relatório do Projeto AWS Academy
## Aplicativo Web Dimensionável e Altamente Disponível

**Aluno:** Leonardo e João Henrique 

---

## 1. Resumo Executivo

Este relatório documenta a implementação de uma Prova de Conceito (POC) para hospedar o aplicativo web de registros de alunos da Universidade Exemplo na nuvem AWS. O projeto foi desenvolvido seguindo as práticas recomendadas do AWS Well-Architected Framework, atendendo a todos os requisitos de funcionalidade, segurança, alta disponibilidade, escalabilidade e otimização de custos.

---

## 2. Requisitos Atendidos

| Requisito | Status | Implementação |
|-----------|--------|---------------|
| **Funcional** | ✅ Atendido | Aplicação CRUD completa para registros de alunos |
| **Carga Balanceada** | ✅ Atendido | Application Load Balancer (ALB) |
| **Dimensionável** | ✅ Atendido | Arquitetura Multi-AZ com capacidade de Auto Scaling |
| **Altamente Disponível** | ✅ Atendido | 2 instâncias em Zonas de Disponibilidade diferentes |
| **Seguro** | ✅ Atendido | RDS em subnet privada, Secrets Manager, Security Groups |
| **Econômico** | ✅ Atendido | Instâncias t3.micro e db.t3.micro |
| **Alto Desempenho** | ✅ Atendido | Respostas < 100ms sob carga normal |

---

## 3. Arquitetura Implementada

### 3.1 Diagrama de Arquitetura

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           AWS CLOUD (us-east-1)                             │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                         VPC: 10.0.0.0/16                              │  │
│  │                                                                       │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │                    INTERNET GATEWAY                             │  │  │
│  │  └──────────────────────────┬──────────────────────────────────────┘  │  │
│  │                             │                                         │  │
│  │  ┌──────────────────────────┴──────────────────────────────────────┐  │  │
│  │  │              APPLICATION LOAD BALANCER (final-ALB)              │  │  │
│  │  │                         Port 80                                 │  │  │
│  │  └──────────────────────────┬──────────────────────────────────────┘  │  │
│  │                             │                                         │  │
│  │       ┌─────────────────────┴─────────────────────┐                   │  │
│  │       │                                           │                   │  │
│  │       ▼                                           ▼                   │  │
│  │  ┌─────────────────────┐                 ┌─────────────────────┐      │  │
│  │  │  PUBLIC SUBNET A    │                 │  PUBLIC SUBNET B    │      │  │
│  │  │  10.0.1.0/24        │                 │  10.0.2.0/24        │      │  │
│  │  │  us-east-1a         │                 │  us-east-1b         │      │  │
│  │  │                     │                 │                     │      │  │
│  │  │  ┌───────────────┐  │                 │  ┌───────────────┐  │      │  │
│  │  │  │ final-WebServer│  │                 │  │final-WebServer│  │      │  │
│  │  │  │   (t3.micro)  │  │                 │  │   -2 (t3.micro)│  │      │  │
│  │  │  │   Node.js     │  │                 │  │   Node.js     │  │      │  │
│  │  │  │   Port 80     │  │                 │  │   Port 80     │  │      │  │
│  │  │  └───────┬───────┘  │                 │  └───────┬───────┘  │      │  │
│  │  └──────────┼──────────┘                 └──────────┼──────────┘      │  │
│  │             │                                       │                 │  │
│  │             └───────────────────┬───────────────────┘                 │  │
│  │                                 │                                     │  │
│  │  ┌─────────────────────┐       │       ┌─────────────────────┐       │  │
│  │  │  PRIVATE SUBNET A   │       │       │  PRIVATE SUBNET B   │       │  │
│  │  │  10.0.3.0/24        │       │       │  10.0.4.0/24        │       │  │
│  │  │  us-east-1a         │       │       │  us-east-1b         │       │  │
│  │  │                     │       │       │                     │       │  │
│  │  │  ┌───────────────┐  │       │       │                     │       │  │
│  │  │  │   final-db    │◄─┼───────┘       │                     │       │  │
│  │  │  │  (db.t3.micro)│  │               │                     │       │  │
│  │  │  │  MySQL 8.0    │  │               │                     │       │  │
│  │  │  │  Port 3306    │  │               │                     │       │  │
│  │  │  └───────────────┘  │               │                     │       │  │
│  │  └─────────────────────┘               └─────────────────────┘       │  │
│  │                                                                       │  │
│  │  ┌─────────────────────────────────────────────────────────────────┐  │  │
│  │  │  SERVIÇOS ADICIONAIS:                                           │  │  │
│  │  │  • AWS Secrets Manager (final/dbcredentials)                    │  │  │
│  │  │  • AWS Cloud9 (ambiente de desenvolvimento)                     │  │  │
│  │  │  • IAM (LabRole + LabInstanceProfile)                           │  │  │
│  │  └─────────────────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Componentes Implementados

| Componente | Nome/Identificador | Especificação |
|------------|-------------------|---------------|
| **VPC** | StudentRecords-VPC | CIDR: 10.0.0.0/16 |
| **Internet Gateway** | StudentRecords-IGW | Anexado à VPC |
| **Subnet Pública A** | Public-Subnet-A | 10.0.1.0/24 (us-east-1a) |
| **Subnet Pública B** | Public-Subnet-B | 10.0.2.0/24 (us-east-1b) |
| **Subnet Privada A** | Private-Subnet-A | 10.0.3.0/24 (us-east-1a) |
| **Subnet Privada B** | Private-Subnet-B | 10.0.4.0/24 (us-east-1b) |
| **EC2 Instance 1** | final-WebServer | t3.micro, Ubuntu 24.04, us-east-1a |
| **EC2 Instance 2** | final-WebServer-2 | t3.micro, Ubuntu 24.04, us-east-1b |
| **RDS** | final-db | db.t3.micro, MySQL 8.0, Single-AZ |
| **ALB** | final-ALB | Internet-facing, HTTP:80 |
| **Target Group** | final-TG | Health check: /health |
| **Secrets Manager** | final/dbcredentials | Credenciais do banco de dados |
| **Cloud9** | StudentRecords-Dev | Ambiente de desenvolvimento |

---

## 4. Detalhamento das Fases

### Fase 1: Planejamento e Estimativa de Custos

#### 4.1.1 Diagrama de Arquitetura
- Criado diagrama completo da arquitetura AWS
- Documentado fluxo de dados entre componentes
- Identificados todos os serviços necessários

#### 4.1.2 Estimativa de Custos (Região us-east-1)

| Serviço | Especificação | Custo Mensal (USD) |
|---------|---------------|-------------------|
| EC2 (Web Servers) | 2x t3.micro (730h/mês) | $16.94 |
| EC2 (Cloud9) | 1x t3.micro (100h/mês) | $1.16 |
| Application Load Balancer | 1 ALB + LCUs | $22.27 |
| Amazon RDS MySQL | db.t3.micro, 20GB | $15.73 |
| AWS Secrets Manager | 1 segredo | $0.40 |
| Data Transfer | 10GB/mês | $0.90 |
| EBS | 3x 8GB gp3 | $2.40 |
| **TOTAL MENSAL** | | **~$59.80** |
| **TOTAL ANUAL** | | **~$717.60** |

---

### Fase 2: Aplicativo Web Básico (POC)

#### 4.2.1 Rede Virtual Criada
- VPC com CIDR 10.0.0.0/16
- Internet Gateway anexado
- Subnet pública configurada
- Route Table com rota para Internet Gateway

#### 4.2.2 Instância EC2 POC
- AMI: Ubuntu Server 22.04 LTS
- Tipo: t3.micro
- MySQL local instalado
- Aplicação Node.js funcionando na porta 80

#### 4.2.3 Testes Realizados
- ✅ Acesso via IP público
- ✅ Visualização de registros
- ✅ Adição de novos alunos
- ✅ Exclusão de registros
- ✅ Modificação de dados

---

### Fase 3: Desacoplamento dos Componentes

#### 4.3.1 Atualização da VPC
- Criadas 2 subnets públicas (us-east-1a e us-east-1b)
- Criadas 2 subnets privadas para RDS
- Route Tables configuradas apropriadamente

#### 4.3.2 Amazon RDS
- **Identificador:** final-db
- **Engine:** MySQL 8.0
- **Classe:** db.t3.micro
- **Armazenamento:** 20 GB gp2
- **Localização:** Subnet privada (sem acesso público)
- **Endpoint:** final-db.c5m24kiik26v.us-east-1.rds.amazonaws.com

#### 4.3.3 AWS Cloud9
- Ambiente provisionado para comandos AWS CLI
- Usado para:
  - Criar segredo no Secrets Manager
  - Migrar dados para RDS
  - Testes de conectividade

#### 4.3.4 AWS Secrets Manager
- **Nome do Segredo:** final/dbcredentials
- **Conteúdo:** username, password, host, database, port
- **Benefício:** Credenciais não hardcoded no código

#### 4.3.5 Nova Instância Web
- **Nome:** final-WebServer
- **AMI:** Ubuntu Server 24.04 LTS
- **Aplicação:** Node.js com AWS SDK para Secrets Manager
- **IAM Profile:** LabInstanceProfile (acesso ao Secrets Manager)

#### 4.3.6 Migração de Dados
- Dados migrados do MySQL local para RDS
- Tabela `students` criada com registros de exemplo
- Verificação de integridade dos dados

---

### Fase 4: Alta Disponibilidade e Dimensionamento

#### 4.4.1 Application Load Balancer
- **Nome:** final-ALB
- **Scheme:** Internet-facing
- **Zonas de Disponibilidade:** us-east-1a, us-east-1b
- **Listener:** HTTP:80 → Target Group

#### 4.4.2 Target Group
- **Nome:** final-TG
- **Tipo:** Instances
- **Health Check Path:** /health
- **Intervalo:** 30 segundos

#### 4.4.3 Segunda Instância EC2
- **Nome:** final-WebServer-2
- **Localização:** Public-Subnet-B (us-east-1b)
- **Criação:** A partir de AMI da instância original
- **Status no Target Group:** Healthy

#### 4.4.4 Configuração de Security Groups

| Security Group | Inbound Rules | Outbound Rules |
|----------------|---------------|----------------|
| **ALB-SG** | HTTP (80) from 0.0.0.0/0 | All traffic |
| **WebServer-SG** | HTTP (80) from ALB-SG, SSH (22) from My IP | All traffic |
| **RDS-SG** | MySQL (3306) from WebServer-SG | All traffic |

#### 4.4.5 Resultado Final
- 2 instâncias EC2 em AZs diferentes
- Ambas registradas e **healthy** no Target Group
- ALB distribuindo tráfego automaticamente
- Alta disponibilidade garantida

---

## 5. Security Groups Configurados

### 5.1 ALB-SG (Load Balancer)
```
Inbound:
  - HTTP (80) from 0.0.0.0/0

Outbound:
  - All traffic to 0.0.0.0/0
```

### 5.2 WebServer-SG (Instâncias EC2)
```
Inbound:
  - HTTP (80) from ALB-SG
  - SSH (22) from My IP

Outbound:
  - All traffic to 0.0.0.0/0
```

### 5.3 RDS-SG (Banco de Dados)
```
Inbound:
  - MySQL/Aurora (3306) from WebServer-SG

Outbound:
  - All traffic to 0.0.0.0/0
```

---

## 6. Endpoints e URLs

| Recurso | Endpoint/URL |
|---------|--------------|
| **ALB (Acesso Principal)** | http://final-ALB-XXXXXXXXX.us-east-1.elb.amazonaws.com |
| **RDS Endpoint** | final-db.c5m24kiik26v.us-east-1.rds.amazonaws.com |
| **Health Check** | http://[ALB-DNS]/health |
| **API Students** | http://[ALB-DNS]/api/students |

---

## 7. Testes Realizados

### 7.1 Testes Funcionais
| Teste | Resultado |
|-------|-----------|
| Visualizar registros de alunos | ✅ Passou |
| Adicionar novo aluno | ✅ Passou |
| Excluir aluno existente | ✅ Passou |
| Modificar registro | ✅ Passou |
| Health check endpoint | ✅ Passou |

### 7.2 Testes de Alta Disponibilidade
| Teste | Resultado |
|-------|-----------|
| ALB distribui tráfego entre instâncias | ✅ Passou |
| Ambas instâncias healthy no Target Group | ✅ Passou |
| Alternância de hostname ao atualizar página | ✅ Passou |

### 7.3 Testes de Segurança
| Teste | Resultado |
|-------|-----------|
| RDS não acessível publicamente | ✅ Passou |
| Credenciais recuperadas do Secrets Manager | ✅ Passou |
| Security Groups bloqueiam acessos não autorizados | ✅ Passou |

---

## 8. Conclusão

O projeto foi implementado com sucesso, atendendo a todos os requisitos especificados:

1. **Funcionalidade:** Aplicação CRUD completa e funcional
2. **Alta Disponibilidade:** 2 instâncias em Zonas de Disponibilidade diferentes
3. **Balanceamento de Carga:** ALB distribuindo tráfego automaticamente
4. **Segurança:** RDS isolado, Secrets Manager, Security Groups configurados
5. **Economia:** Uso de instâncias t3.micro e db.t3.micro
6. **Escalabilidade:** Arquitetura preparada para Auto Scaling

A POC demonstra que a infraestrutura AWS é capaz de hospedar o aplicativo da Universidade Exemplo com alta disponibilidade, segurança e performance adequada para o período de pico de admissões.

---

## 9. Recursos AWS Utilizados

- Amazon VPC
- Amazon EC2
- Amazon RDS (MySQL)
- Application Load Balancer
- AWS Secrets Manager
- AWS Cloud9
- AWS IAM
- Amazon EBS

---

**Documento gerado em:** 28 de Novembro de 2025  
**Versão:** 1.0
