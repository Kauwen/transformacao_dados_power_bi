# transformacao_dados_power_bi

# Ajustando os códigos para Azure MySQL
Foi necessário ajustar os códigos, devido a não estar corretamente organizados para aplicação ao projeto. Assim, segue os códigos ajustados:

```ruby
create schema if not exists azure_company;
```
---
```ruby
select * from information_schema.table_constraints
where constraint_schema = 'azure_company';
```
---
```ruby
CREATE TABLE employee(
  Fname varchar(15) not null,
  Minit char,
  Lname varchar(15) not null,
  Ssn char(9) not null, 
  Bdate date,
  Address varchar(30),
  Sex char,
  Salary decimal(10,2),
  Super_ssn char(9),
  Dno int not null,
  constraint chk_salary_employee check (Salary> 2000.0),
  constraint pk_employee primary key (Ssn)
);
```
---
```ruby
alter table employee modify Dno int not null default 1;
```
---
```ruby
create table departament(
  Dname varchar(15) not null,
  Dnumber int not null,
  Mgr_ssn char(9) not null,
  Mgr_start_date date, 
  Dept_create_date date,
  constraint chk_date_dept check (Dept_create_date < Mgr_start_date),
  constraint pk_dept primary key (Dnumber),
  constraint unique_name_dept unique(Dname),
  foreign key (Mgr_ssn) references employee(Ssn)
);
```
---
```ruby
alter table departament add constraint fk_dept foreign key(Mgr_ssn) references employee(Ssn) on update cascade;
```
---
```ruby
create table dept_locations(
  Dnumber int not null,
  Dlocation varchar(15) not null,
  constraint pk_dept_locations primary key (Dnumber, Dlocation)
);
```
---
```ruby
alter table dept_locations 
add constraint fk_dept_locations foreign key (Dnumber) references departament(Dnumber)
on delete cascade
on update cascade;
```
---
```ruby
create table project(
  Pname varchar(15) not null,
  Pnumber int not null,
  Plocation varchar(15),
  Dnum int not null,
  primary key (Pnumber),
  constraint unique_project unique (Pname),
  constraint fk_project foreign key (Dnum) references departament(Dnumber)
);
```
---
```ruby
create table works_on(
  Essn char(9) not null,
  Pno int not null,
  Hours decimal(3,1) not null,
  primary key (Essn, Pno),
  constraint fk_employee_works_on foreign key (Essn) references employee(Ssn),
  constraint fk_project_works_on foreign key (Pno) references project(Pnumber)
);
```
---
```ruby
create table dependent(
  Essn char(9) not null,
  Dependent_name varchar(15) not null,
  Sex char,
  Bdate date,
  Relationship varchar(8),
  primary key (Essn, Dependent_name),
  constraint fk_dependent foreign key (Essn) references employee(Ssn)
);
```

# Realizando link com Workbench
Após criar as tabelas foi necessário realizar a conexão do projeto no Azure com Workbench

# Aplicando a transformação dos dados pelo Power Bi
1. Verificar os cabeçalhos e tipos de dados
2. Modificar os valores monetários para o tipo double preciso
3. Verificar a existência dos nulos e analise a remoção
4. Os employees com nulos em Super_ssn podem ser os gerentes. Verificar se há algum colaborador sem gerente
5. Verificar se há algum departamento sem gerente
6. Se houver departamento sem gerente, adiconar os dados e preencher as lacunas
7. Verificar o número de horas dos projetos
8. Separar colunas complexas
9. Mesclar consultas employee e departament para criar uma tabela employee com o nome dos departamentos associados aos colaboradores. A mescla terá como base a tabela employee. Ficar atento, essa informação influencia no tipo de junção
10. Neste processo eliminar as colunas desnecessárias.
11. Realizar a junção dos colaboradores e respectivos nomes dos gerentes . Isso pode ser feito com consulta SQL ou pela mescla de tabelas com Power BI. Caso utilize SQL, especifique no README a query utilizada no processo.
12. Mesclar as colunas de Nome e Sobrenome para ter apenas uma coluna definindo os nomes dos colaboradores
13. Mesclar os nomes de departamentos e localização. Isso fará que cada combinação departamento-local seja único. Isso irá auxiliar na criação do modelo estrela em um módulo futuro.
14. Explicar por que, neste caso supracitado, podemos apenas utilizar o mesclar e não o atribuir.
15. Agrupar os dados a fim de saber quantos colaboradores existem por gerente
16. Eliminar as colunas desnecessárias, que não serão usadas no relatório, de cada tabela
