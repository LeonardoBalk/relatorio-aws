# Guia de Apresentação em Slides
## Projeto AWS Academy - Aplicativo Web Dimensionável e Altamente Disponível

---

## 📊 Estrutura Sugerida dos Slides

### **Total: 12-15 slides | Tempo: 10-15 minutos**

---

## SLIDE 1: Capa
```
TÍTULO: Aplicativo Web Dimensionável e Altamente Disponível

SUBTÍTULO: Projeto AWS Academy - Prova de Conceito

ALUNO: Leonardo
DATA: Novembro 2025
```

---

## SLIDE 2: Agenda
```
AGENDA:

1. Visão Geral do Projeto
2. Arquitetura Implementada
3. Fase 1 - Planejamento
4. Fase 2 - POC Básica
5. Fase 3 - Desacoplamento
6. Fase 4 - Alta Disponibilidade
7. Demonstração ao Vivo
8. Estimativa de Custos
9. Conclusão
```

---

## SLIDE 3: O Problema
```
O DESAFIO:

📚 Universidade Exemplo - Sistema de Registros de Alunos

PROBLEMAS:
• Aplicativo lento durante período de admissões
• Indisponibilidade em horários de pico
• Milhares de usuários simultâneos
• Necessidade de alta disponibilidade

OBJETIVO:
Criar uma POC na AWS que seja:
✅ Funcional  ✅ Escalável  ✅ Segura  ✅ Econômica
```

---

## SLIDE 4: Requisitos da Solução
```
REQUISITOS ATENDIDOS:

✅ Funcional      → CRUD completo (visualizar, adicionar, excluir, modificar)
✅ Carga Balanceada → Application Load Balancer
✅ Dimensionável   → Multi-AZ + preparado para Auto Scaling
✅ Alta Disponibilidade → 2 instâncias em AZs diferentes
✅ Seguro         → RDS privado + Secrets Manager + Security Groups
✅ Econômico      → Instâncias t3.micro (~$60/mês)
✅ Alto Desempenho → Respostas rápidas sob carga
```

---

## SLIDE 5: Diagrama de Arquitetura
```
[INSERIR DIAGRAMA DE ARQUITETURA]

Componentes principais:
• VPC com 4 subnets (2 públicas, 2 privadas)
• 2 EC2 instances (us-east-1a e us-east-1b)
• Application Load Balancer
• RDS MySQL em subnet privada
• Secrets Manager para credenciais
```

**DICA:** Use o diagrama do documento principal ou desenhe no draw.io/Lucidchart

---

## SLIDE 6: Fase 1 - Planejamento
```
FASE 1: PLANEJAMENTO

📐 Diagrama de Arquitetura
   • Identificação de todos os componentes AWS
   • Fluxo de dados entre serviços
   • Zonas de disponibilidade

💰 Estimativa de Custos
   • AWS Pricing Calculator
   • Custo mensal: ~$59.80
   • Custo anual: ~$717.60

📋 Serviços selecionados com base em:
   • Requisitos do projeto
   • Restrições de orçamento
   • Práticas recomendadas AWS
```

---

## SLIDE 7: Fase 2 - POC Básica
```
FASE 2: APLICATIVO WEB BÁSICO

🌐 Rede Virtual (VPC)
   • CIDR: 10.0.0.0/16
   • Internet Gateway
   • Subnet pública

💻 Instância EC2
   • Ubuntu Server
   • MySQL local
   • Aplicação Node.js
   • Porta 80

✅ Testes realizados:
   • Acesso via IP público
   • CRUD funcionando
```

---

## SLIDE 8: Fase 3 - Desacoplamento
```
FASE 3: DESACOPLAMENTO DOS COMPONENTES

🗄️ Amazon RDS
   • MySQL 8.0 em subnet privada
   • Sem acesso público (segurança!)
   • db.t3.micro

🔐 AWS Secrets Manager
   • Credenciais armazenadas de forma segura
   • Não hardcoded no código
   • Acesso via IAM Role

☁️ AWS Cloud9
   • Ambiente de desenvolvimento
   • Execução de scripts AWS CLI
   • Migração de dados
```

---

## SLIDE 9: Fase 4 - Alta Disponibilidade
```
FASE 4: ALTA DISPONIBILIDADE

⚖️ Application Load Balancer
   • Distribui tráfego automaticamente
   • Health checks a cada 30s
   • Endpoint público único

🖥️ Multi-AZ Deployment
   • final-WebServer    → us-east-1a
   • final-WebServer-2  → us-east-1b

🎯 Target Group
   • Ambas instâncias HEALTHY
   • Path: /health
   • Failover automático
```

---

## SLIDE 10: Segurança Implementada
```
SEGURANÇA:

🔒 Security Groups (Firewall)
   • ALB-SG: Aceita HTTP da internet
   • WebServer-SG: Aceita HTTP apenas do ALB
   • RDS-SG: Aceita MySQL apenas dos WebServers

🗄️ Banco de Dados
   • RDS em subnet PRIVADA
   • Sem IP público
   • Acesso apenas interno

🔑 Credenciais
   • Armazenadas no Secrets Manager
   • Recuperadas via AWS SDK
   • IAM Role para autorização
```

---

## SLIDE 11: Demonstração ao Vivo
```
DEMONSTRAÇÃO:

1️⃣ Acessar o ALB
   → http://final-ALB-xxx.us-east-1.elb.amazonaws.com

2️⃣ Mostrar alternância de instâncias
   → Atualizar página e observar hostname

3️⃣ Testar CRUD
   → Adicionar um aluno
   → Excluir um aluno

4️⃣ Mostrar no Console AWS:
   → Target Group com 2 instâncias healthy
   → RDS em subnet privada
   → Secrets Manager
```

---

## SLIDE 12: Estimativa de Custos
```
ESTIMATIVA DE CUSTOS (us-east-1):

| Serviço                | Custo Mensal |
|------------------------|--------------|
| EC2 (2x t3.micro)      | $16.94       |
| Application Load Balancer | $22.27    |
| RDS MySQL (db.t3.micro)| $15.73       |
| Secrets Manager        | $0.40        |
| EBS + Data Transfer    | $4.46        |
|------------------------|--------------|
| TOTAL MENSAL           | ~$59.80      |
| TOTAL ANUAL            | ~$717.60     |
```

---

## SLIDE 13: Recursos AWS Utilizados
```
SERVIÇOS AWS:

☁️ Computação
   • Amazon EC2 (2 instâncias)
   • AWS Cloud9

🌐 Rede
   • Amazon VPC (4 subnets)
   • Application Load Balancer
   • Internet Gateway

🗄️ Banco de Dados
   • Amazon RDS (MySQL)

🔐 Segurança
   • AWS Secrets Manager
   • Security Groups
   • IAM Roles
```

---

## SLIDE 14: Conclusão
```
CONCLUSÃO:

✅ TODOS OS REQUISITOS ATENDIDOS

• Aplicação funcional e responsiva
• Alta disponibilidade com Multi-AZ
• Balanceamento de carga automático
• Segurança em camadas
• Custo otimizado (~$60/mês)

🎯 A POC está pronta para demonstrar que a AWS
   pode hospedar o sistema da universidade com
   qualidade e confiabilidade!
```

---

## SLIDE 15: Perguntas
```
OBRIGADO!

🙋 Perguntas?


Contato: [seu email]
GitHub: [se tiver repositório]
```

---

## 📋 DICAS PARA A APRESENTAÇÃO

### Tempo por Slide:
- Slides 1-2: 30 segundos cada
- Slides 3-4: 1 minuto cada
- Slide 5 (Arquitetura): 2 minutos
- Slides 6-9 (Fases): 1-2 minutos cada
- Slide 10 (Segurança): 1 minuto
- Slide 11 (Demo): 3-4 minutos
- Slides 12-15: 30 segundos a 1 minuto cada

### O que mostrar no Console AWS durante a demo:
1. **EC2 > Instances** - Mostrar as 2 instâncias rodando
2. **EC2 > Target Groups** - Mostrar ambas healthy
3. **EC2 > Load Balancers** - Mostrar o ALB ativo
4. **RDS > Databases** - Mostrar o banco em subnet privada
5. **Secrets Manager** - Mostrar o segredo criado
6. **VPC > Subnets** - Mostrar as 4 subnets

### Perguntas que o professor pode fazer:
1. "Por que usar ALB em vez de NLB?" 
   → ALB é para HTTP/HTTPS, NLB para TCP/UDP de baixa latência

2. "Por que o RDS está em subnet privada?"
   → Segurança - banco não deve ser acessível da internet

3. "Como as credenciais são protegidas?"
   → Secrets Manager + IAM Role (não hardcoded)

4. "O que acontece se uma instância falhar?"
   → ALB detecta via health check e direciona para a outra

5. "Como escalar para mais instâncias?"
   → Criar Auto Scaling Group com a AMI existente

---

## 🎨 FERRAMENTAS PARA CRIAR OS SLIDES

1. **PowerPoint** - Tradicional, funciona offline
2. **Google Slides** - Gratuito, colaborativo
3. **Canva** - Templates bonitos e fáceis
4. **Prezi** - Apresentação dinâmica (diferente)

### Template AWS:
- Use cores AWS: Laranja (#FF9900) e Azul escuro (#232F3E)
- Ícones AWS: https://aws.amazon.com/architecture/icons/

---

**Boa apresentação! 🚀**
