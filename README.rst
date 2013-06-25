**************
django-tenancy
**************

Handle multi-tenancy in Django with no additional global state using schemas.

http://pypi.python.org/pypi/django-tenancy

Installation
============
Assuming you have django installed, the first step is to install *django-tenancy*:
::

 pip install django-tenany

Now you have access the ``tenancy`` module in your Django project. 

Using django-tenancy
====================

Define the Tenant Model
-----------------------

First you'll create you tenant model. The tenant model will be a subclass of the ``AbstractTenant`` class provided by *django-tenancy*.

For instance, your ``myapp/models.py`` will look like:
:: 
   
   from tenancy.models import AbstractTenant

   class MyTenantModel(AbstractTenant):
      name = models.CharField(max_length=50)
      # other fields
      def natural_key(self):
         return ((self.name, ))

**Important note**: the ``natural_key`` method must return a tuple that will be used to prefix the model and its database table. This prefix must be unique to the tenant.

Declare the Tenant Model
--------------------------
Now that you have your tenant model, let's declare it for your project in *settings.py*:
::

 TENANCY_TENANT_MODEL=myapp.MyTenantModel

Run a database synchronization to create the corresponding table:
::

 python manage.py syncdb

Define the tenant-specific models
-----------------------------------
The tenant-specific models will subclass the ``TenantModel`` class provided by *django-tenancy*.

For instance, each tenant will have projects and reports. Here is how ``myapp/models.py`` will look like:
:: 
   
   from tenancy.models import AbstractTenant

   class MyTenantModel(AbstractTenant):
      name = models.CharField(max_length=50)
      # other fields
      def natural_key(self):
         return ((self.name, ))

   class Project(TenantModel):
      name = models.CharField(max_length=50)
      description = models.CharField(max_length=300, blank=True, null=True)

   class Report(TenantModel):
      name = models.CharField(max_length=50)
      content = models.CharField(max_length=300, blank=True, null=True)

Playing with the models
---------------------------------------------------
You can manipulate the tenant and tenant-specific models as any other Django models.

Create a tenant instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

 tenant = MyTenantModel.objects.create("myfirsttenant")

Get a tenant-specific model: for_tenant()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
<TenantModel>.for_tenant(<AbstractTenant instance>)

``TenantModel`` comes with a method that allows you to get the specific TenantModel of a given Tenant instance. For instance:
::

 tenant_project = Project.for_tenant(tenant)

Create a tenant-specific instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
::

 tenant_project.objects.create("myfirsttenant_project")

