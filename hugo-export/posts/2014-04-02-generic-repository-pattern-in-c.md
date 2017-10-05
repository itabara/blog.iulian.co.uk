---
title: 'Generic Repository Pattern in C#'
author: Iulian
type: post
date: 2014-04-02T18:04:59+00:00
url: /2014/04/generic-repository-pattern-in-c/
categories:
  - Design patterns

---
<div class="row">
  <div id="post-area" class="col span_9">
    <article id="post-2322" class="post-2322 post type-post status-publish format-standard has-post-thumbnail hentry category-c_ tag-c"> 
    
    <div class="post-content">
      <div class="content-inner">
        <p style="text-align: justify;">
          It&#8217;s a good practice to separate data access layer from business logic. Tight coupling of the database logic in the business logic make applications difficult to test and extend further. Direct access of the data in the business logic may cause problems such as:
        </p>
        
        <ol>
          <li>
            Difficulty completing Unit Test of the business logic
          </li>
          <li style="text-align: justify;">
            Business logic cannot be tested without the dependencies of external systems like database
          </li>
          <li style="text-align: justify;">
            Duplicate data access code throughout the business layer
          </li>
          <li style="text-align: justify;">
            Difficulty to change the database provider (EntityFramework can be a solution).
          </li>
        </ol>
        
        <p style="text-align: justify;">
          <a class="no-ajaxy" href="http://martinfowler.com/eaaCatalog/repository.html">Repository Pattern</a> separates the data access logic and maps it to the entities in the business logic. It works with the domain entities and performs data access logic. In the Repository pattern, the domain entities, the data access logic and the business logic talk to each other using interfaces. It hides the details of data access from the business logic. In other words, business logic can access the data object without having knowledge of the underlying data access architecture. For example, in the Repository pattern, business logic is not aware whether the application is using LINQ to SQL or ADO.NET Entity Framework. In the future, underlying data sources or architecture can be changed without affecting the business logic.
        </p>
        
        <p>
          There are various advantages of the Repository Pattern including:
        </p>
        
        <ul>
          <li style="text-align: justify;">
            Business logic can be tested without need for an external source
          </li>
          <li style="text-align: justify;">
            Database access logic can be tested separately
          </li>
          <li style="text-align: justify;">
            No duplication of code
          </li>
          <li style="text-align: justify;">
            Caching strategy for the datasource can be centralized
          </li>
          <li style="text-align: justify;">
            Domain driven development is easier
          </li>
          <li style="text-align: justify;">
            Centralizing the data access logic, so code maintainability is easier
          </li>
        </ul>
        
        <p style="text-align: justify;">
          Let&#8217;s consider the base <strong>IEntityBase.cs</strong> for each entity in the database.
        </p>
        
        <pre class="lang:c# decode:true" title="IEntityBase">public class IEntityBase
{
        public string Id; 
}</pre>
        
        <p>
          Generic Repository Interface of the type IEntityBase can be created as follows. I have added basic CRUD operations as part of it. Keep in mind that if a particular repository requires additional operations, it can extend the generic repository interface.
        </p>
        
        <pre class="lang:c# decode:true" title="Generic Repository">public interface IBaseRepository&lt;T&gt; where T: IEntityBase
{
    IEnumerable&lt;T&gt; List { get; }
    void Add(T entity);
    void Delete(T entity);
    void Update(T entity);
    T FindById(int Id);
}</pre>
        
        <p>
          We are going to work on the Employee table. To represent this, we have created an entity class Employee. Employee entity class should extend the <strong>IEntityBase</strong> class to work with the generic repository.
        </p>
        
        <pre class="lang:c# decode:true">[Table("Employee")]
public partial class Employee : IEntityBase
{
    public int Id { get; set; }
    
    [Required]
    public string FirstName { get; set; }
    
    [Required]
    public string LastName { get; set; }
}</pre>
        
        <p>
          To create <strong>EmployeeRepository,</strong> create a class that will implement the generic repository interface <strong>IRepository<Employee></strong>. I am performing CRUD operations using the <strong>Entity Framework</strong>. However, you may use any option like LINQ to SQL, ADO.NET etc.
        </p>
      </div>
      
      <pre class="lang:c# decode:true">public class EmployeeRepository : IRepository&lt;Employee&gt;
{
     Model _employeeContext;
 
     public EmployeeRepository()
     {
         _employeeContext = new Model();
     }
     
     public IEnumerable&lt;Employee&gt; List
     {
        get
        {
           return _employeeContext.Employees;
        }
     }
 
     public void Add(Employee entity)
     {
            _employeeContext.Employees.Add(entity);
            _employeeContext.SaveChanges();
     }
 
    public void Delete(Employee entity)
    {
            _employeeContext.Employees.Remove(entity);
            _employeeContext.SaveChanges();
    }
 
    public void Update(Employee entity)
    {
        _employeeContext.Entry(entity).State = System.Data.Entity.EntityState.Modified;
        _employeeContext.SaveChanges();
    }
 
    public Employee FindById(int Id)
    {
            var result = (from r in _employeeContext.Employees where r.Id == Id select r).FirstOrDefault();
            return result; 
    }
}</pre>
      
      <p>
        The implemented generic repository can be used as follows:
      </p>
      
      <pre class="lang:c# decode:true " title="Using repository">IRepository&lt;Employee&gt; repository = new EmployeeRepository();
var result = repository.List;
foreach (var r in result)
{
     Console.WriteLine(r.FirstName);
}</pre>
      
      <p>
        &nbsp;
      </p>
    </div></article>
  </div>
</div>