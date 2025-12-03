
## Demonstração da Solução

### Aplicação Web Funcionando
> Acesse via DNS do ALB: `http://final-alb-91048321.us-east-1.elb.amazonaws.com/`

<img width="1920" height="1156" alt="image" src="https://github.com/user-attachments/assets/28550835-8ddc-4ec6-807e-52b9551c198f" />


*Interface do sistema de registros de alunos com funcionalidades de cadastro, visualização e exclusão.*

---

### Application Load Balancer
> Console AWS → EC2 → Load Balancers

<img width="1618" height="722" alt="image" src="https://github.com/user-attachments/assets/c99d5baa-989f-45bc-b02c-e4d3656984ed" />


*Load Balancer ativo distribuindo tráfego entre as instâncias.*

---

### Target Group - Instâncias Healthy
> Console AWS → EC2 → Target Groups

<img width="1618" height="722" alt="image" src="https://github.com/user-attachments/assets/df4f0380-14fa-42ed-8467-753a79f624d0" />


*Duas instâncias em zonas de disponibilidade diferentes, ambas com status "healthy".*

---

### Auto Scaling Group
> Console AWS → EC2 → Auto Scaling Groups

<img width="1618" height="722" alt="image" src="https://github.com/user-attachments/assets/06c2271d-067c-408d-99b7-89378eacd6d7" />


*Configuração de Auto Scaling: Mínimo 2, Máximo 6, Target CPU 50%.*

---

### RDS em Subnet Privada
> Console AWS → RDS → Databases

<img width="1920" height="2498" alt="image" src="https://github.com/user-attachments/assets/0d142a2e-7d3d-4f5a-a488-0e98d398e293" />


*Banco de dados MySQL isolado em subnet privada, sem acesso público.*

---

### AWS Secrets Manager
> Console AWS → Secrets Manager

<img width="1920" height="1750" alt="image" src="https://github.com/user-attachments/assets/f005ae8c-22fc-4f2a-99bb-99deaaf21c9b" />


*Credenciais do banco armazenadas de forma segura.*








<div align="center">

**⭐ Se este projeto foi útil, considere dar uma estrela no repositório! ⭐**

</div>
