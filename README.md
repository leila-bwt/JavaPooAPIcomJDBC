# Curso: Programação Orientada a Objetos com Java #
  Prof. Dr. Nelio Alves - Educando Web


## Capítulo: Acesso a banco de dados com JDBC ##

**Objetivo geral:**
 Conhecer os principais recursos do JDBC na teoria e prática
 Elaborar a estrutura básica de um projeto com JDBC
 Implementar o padrão DAO manualmente com JDBC

**Visão geral do JDBC**
 JDBC (Java Database Connectivity): API padrão do Java para acesso a dados
 Páginas oficiais: 
o https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/
o https://docs.oracle.com/javase/8/docs/api/java/sql/package-summary.html
 Pacotes: java.sql e javax.sql (API suplementar para servidores)

![esquemaAPIJava1](https://github.com/leila-bwt/DesafioII_API_REST_Cubos_Academi/assets/108028195/9d5c90f0-e375-4bb6-9291-8ba2bcd32f55)

*Instalação das ferramentas:*
 Instalar o MySQL Server e o MySQL Workbench


## Preparação do primeiro projeto no Eclipse
Código fonte: https://github.com/acenelio/jdbc1

*Checklist:*
* Usando o MySQL Workbench, crie uma base de dados chamada "coursejdbc"
* Baixar o MySQL Java Connector
* Caso ainda não exista, criar uma User Library contendo o arquivo .jar do driver do MySQL
      o Window -> Preferences -> Java -> Build path -> User Libraries
      o Dê o nome da User Library de MySQLConnector
      o Add external JARs -> (localize o arquivo jar)
* Criar um novo Java Project
      o Acrescentar a User Library MySQLConnector ao projeto
* Na pasta raiz do projeto, criar um arquivo "db.properties" contendo os dados de conexão:
user=usuario
password=senha
dburl=jdbc:mysql://localhost:3306/coursejdbc
useSSL=false
* No pacote "db", criar uma exceção personalizada DbException
* No pacote "db", criar uma classe DB com métodos estáticos auxiliares
      o Obter e fechar uma conexão com o banco


## Demo: recuperar dados
Script SQL: material de apoio ou https://github.com/acenelio/demo-dao-jdbc/blob/master/database.sql
Código fonte: https://github.com/acenelio/jdbc2

*API:*
* Statement
* ResultSet
      o first() [move para posição 1, se houver]
      o beforeFirst() [move para posição 0]
      o next() [move para o próximo, retorna false se já estiver no último]
      o absolute(int) [move para a posição dada, lembrando que dados reais começam em 1]

*Checklist:*
* Usar o script SQL para criar a base de dados "coursejdbc"
* Fazer um pequeno programa para recuperar os departamentos
* Na classe DB, criar métodos auxiliares estáticos para fechar ResultSet e Statement
Atenção: o objeto ResultSet contém os 
dados armazenados na forma de tabela:

![APIJavaMySQLTabela1](https://github.com/leila-bwt/DesafioII_API_REST_Cubos_Academi/assets/108028195/df0cc74e-22c1-441f-a4a4-aa6e29f26e28)


## Demo: inserir dados
Código fonte: https://github.com/acenelio/jdbc3

*API:*
* PreparedStatement
* executeUpdate
* Statement.RETURN_GENERATED_KEYS
* getGeneratedKeys
  
*Checklist:*
* Inserção simples com preparedStatement
* Inserção com recuperação de Id

  
## Demo: atualizar dados
Código fonte: https://github.com/acenelio/jdbc4


## Demo: deletar dados
Código fonte: https://github.com/acenelio/jdbc5

*Checklist:*
* Criar DbIntegrityException
* Tratar a exceção de integridade referencial

  
## Demo: transações
Referências: https://www.ibm.com/support/knowledgecenter/en/SSGMCP_5.4.0/product-overview/acid.html
Código fonte: https://github.com/acenelio/jdbc6

*API:*
* setAutoCommit(false)
*  commit()
* rollback()

## Padrão de projeto DAO (Data Access Object) 
Referências:
https://www.devmedia.com.br/dao-pattern-persistencia-de-dados-utilizando-o-padrao-dao/30999
https://www.oracle.com/technetwork/java/dataaccessobject-138824.html

*Ideia geral do padrão DAO:*
* Para cada entidade, haverá um objeto responsável por fazer acesso a dados relacionado a esta 
entidade. Por exemplo:
      o Cliente: ClienteDao
      o Produto: ProdutoDao
      o Pedido: PedidoDao
* Cada DAO será definido por uma interface.
* A injeção de dependência pode ser feita por meio do padrão de projeto Factory

![projetoDao](https://github.com/leila-bwt/DesafioII_API_REST_Cubos_Academi/assets/108028195/97fb1df7-6e0a-4b75-bfe8-c5ede24e2713)

## Creating project and git repository
Project: http://github.com/acenelio/demo-dao-jdbc

*Checklist:*
* Github: create a new project
      o NOTE: choose .gitignore type as Java
* Eclipse: create new java project with MySQLConnector library
      o Copy db package and db.properties from: https://github.com/acenelio/jdbc5
* Create local repository and push to Github:
      git init
      git remote add origin 
      git pull origin master
      git add .
      git commit -m "Project created"
      git push -u origin master

  
## Department entity class
*Entity class checklist:*
* Attributes
* Constructors
* Getters/Setters
* hashCode and equals
* toString
* implements Serializable

* ![departmentEntityClass](https://github.com/leila-bwt/DesafioII_API_REST_Cubos_Academi/assets/108028195/ebd4c36e-1c5a-4566-b5bf-0ec730be4277)

## Seller entity class
(video)


## DepartmentDao and SellerDao interfaces
(video)


## SellerDaoJDBC and DaoFactory
(video)


## findById implementation
*SQL Query:*
      SELECT seller.*,department.Name as DepName 
      FROM seller INNER JOIN department 
      ON seller.DepartmentId = department.Id 
      WHERE seller.Id = ?

![resultSetTable](https://github.com/leila-bwt/DesafioII_API_REST_Cubos_Academi/assets/108028195/56a59ad2-7093-47ef-9932-7a7ba6c16662)


## Reusing instantiation
private Seller instantiateSeller(ResultSet rs, Department dep) throws SQLException {
      Seller obj = new Seller();
      obj.setId(rs.getInt("Id"));
      obj.setName(rs.getString("Name"));
      obj.setEmail(rs.getString("Email"));
      obj.setBaseSalary(rs.getDouble("BaseSalary"));
      obj.setBirthDate(rs.getDate("BirthDate"));
      obj.setDepartment(dep);
      return obj;
}

private Department instantiateDepartment(ResultSet rs) throws SQLException {
      Department dep = new Department();
      dep.setId(rs.getInt("DepartmentId"));
      dep.setName(rs.getString("DepName"));
      return dep;
}


## findByDepartment implementation
*SQL Query:*
      SELECT seller.*,department.Name as DepName 
      FROM seller INNER JOIN department 
      ON seller.DepartmentId = department.Id
      WHERE DepartmentId = ?
      ORDER BY Name

  ![findBy](https://github.com/leila-bwt/DesafioII_API_REST_Cubos_Academi/assets/108028195/903c8483-6d1b-4c2b-aa6d-8b50b5421b99)


## findAll implementation
*SQL Query:*
      SELECT seller.*,department.Name as DepName 
      FROM seller INNER JOIN department 
      ON seller.DepartmentId = department.Id
      ORDER BY Name

      
## insert implementation
*SQL Query:*
      INSERT INTO seller
      (Name, Email, BirthDate, BaseSalary, DepartmentId) 
      VALUES 
      (?, ?, ?, ?, ?)

      
## update implementation
*SQL Query:*
      UPDATE seller 
      SET Name = ?, Email = ?, BirthDate = ?, BaseSalary = ?, DepartmentId = ? 
      WHERE Id = ?

      
## delete implementation
*SQL Query:*
      DELETE FROM seller 
      WHERE Id = ?

      
## DepartmentDao implementation and test
*Checklist:*
* DepartmentDaoJDBC
* DaoFactory
* Program2
http://github.com/acenelio/demo-dao-jdbc


