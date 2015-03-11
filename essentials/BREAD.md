# B.R.E.A.D

BREAD is a controller action pattern used to map actions against operations that can be performed on a database record/record set. You've probably heard of CRUD, well BREAD is similar, however it breaks the read action out into two, a crucial difference. 

BREAD is an acronym, just like CRUD:

* B - Browse
* R - Read
* E - Edit
* A - Add
* D - Delete

BREAD is implemented within the `KControllerModel` class. This is typically something that can be requested or modified in some way. 

### Browse

The browse action is executed whenever the plural of the view is requested (the controller loaded is always the singular of the view). For example, products, articles, posts. The browse action returns a `KModelEntityRowset` of records returned from the model. 

Browse executed contextually based on the plurality of the view via an HTTP GET request and is dispatched via the RENDER action.

### Read

The read action is executed whenever the singular of the view is requested. For example, product, article, post. The read action always returns a `KModelEntityRow` of the record returned from the model. 

Read is executed contextually based on the singularity of the view via an HTTP GET request and is dispatched via the RENDER action.

### Edit

The edit action modifies an existing record and returns either a 205 (Reset) HTTP status Content, if data was modified, or a 204 (No content) HTTP status if the data was not changed.

Edit is executed contextually based on the uniqueness of the model state. If the model state is unique, then when issuing an HTTP POST request, an edit action will be performed. Edit is dispatched via the POST action.

### Add

The add action modifies an existing record and returns either a 201 (Created) HTTP status, if a new record is created, or an exception is thrown if the record could not be created.

Add is executed contextually based on the uniqueness of the model state. If the model state is unique, then when issuing an HTTP POST request, an add action will be performed. Add is dispached via the POST action.

### Delete

The delete action deletes a record or a set of records. A 204 (No content) HTTP status is returned if the record is deleted, an exception is thrown if not. Delete is dispatched directly via the HTTP Delete method.

---

### Why?

Separating browse and read allows for more flexibility, specifically with testing, behaviors, events, etc. Standardising on the above structure allows a base controller to handle the majority of requests to retrieve or modify a record. 

Most actions that apply to a record fall into the 5 categories above. If you find yourself having to create additional custom actions, then chances are, they can be better represented by the above 5. You should not implement actions like actionPublish() or actionEnable(). Those should be achieved through an edit action and passing the data to change as context. Binding context into your action names decreases flexibility, forces you to write more duplicate code, and makes testing more laborious.

#### Notes:

The default controller extends the model controller, so for most controllers that map to records (e.g. database tables), you do not need to create your controller file/class, Nooku will handle finding the right controller for you. 

Bearing that in mind, if you need to create a view that is not associated with a record, like a static text for example, then you must create a controller class that extends `KControllerView`