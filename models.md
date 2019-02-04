# Models
- [Overview](#overview)
    - [Example Model](#example-model)
    - [Model Properties](#model-properties)
- [Interacting with Data](#interacting-with-data)
    - [Persisting Data](#persisting-data)
    - [Retrieving Data](#retrieving-data)
        - [Query Parameters](#query-parameters)
    - [Updating Data](#updating-data)
    - [Deleting Data](#deleting-data)
- [Other Model Types](#other-model-types)
    - [AssociativeModel](#associative-model)
    - [ReadOnlyModel](#readonly-model)
    - [TemporalModel](#temporal-model)

<a name="overview"></a>
## Overview

Models provide an easy way to interact with your database. Each database table you create would have a corresponding `Model` created to interact with it. By extending the `Intersect\Database\Model\Model` class, you inherit all the core functionality you will need to create, read, update, and delete records in your database.

By default, the table names with be assumed based on the name of your model class. It will try to determine the table name by converting the class name into its pluralized version. You are however able to specify the `$tableName` property yourself to provide a different table name than the assumed one.

Each `Model` also assumes the primary key for each table is the `id` column. You can override this column value by specifying the `primaryKey` property

<a name="example-model"></a>
### Example Model

    <?php

    namespace App;

    use Intersect\Database\Model\Model;

    class Example extends Model {

        // optional property to specify the primary key column
        protected $primaryKey = 'id';

        // optional property to specify table name
        protected $tableName = 'examples';

    }

The `Example` model above is the most basic version you would need to get started interacting with your data.

### Model Properties

The following `Model` properties can be overriden to bypass the default values and behaviors.

| Property | Type | Description |
| --- | --- | --- |
| attributes | array | list of default attribute values the model has |
| columns | array | list of columns associated with table (defaults to actual column list from database when empty) |
| primaryKey | string | the primary key column |
| readOnlyAttributes | array | list of attributes that cannot be updated |
| tableName | string | table name this model will interact with |

<a name="interacting-with-data"></a>
## Interacting with Data

<a name="persisting-data"></a>
### Persisting Data

The `Model` class has a `save` method which allows you to create a new record or update an existing record (if the model has its primary key value populated).

Using the `Example` model from [above](#example-model), this is how you would go about persisting a new `Example` model record in the database. This example assumes you have a table with an "*id*" primary key column and a "*description*" column.

The response of `$newExample` should be a fully populated `Example` model object.

    <?php

    use App\Example;

    $example = new Example();
    $example->description = 'This is my description.';
    $newExample = $example->save();

    echo $newExample->id; // 1 (assumed first record in database)
    echo $newExample->description; // This is my description

<a name="retrieving-data"></a>
### Retrieving Data

Your `Model` comes with helper methods to retrieve data from your database. Here are the method definitions you have access to:

    // returns array of all records based on *optional* QueryParameters object passed in. if null is passed in, returns array of all records
    Example::find(QueryParameters?);

    // returns model matching id value passed in
    Example::findById(123);

    // returns first model found based on *optional* QueryParameters object passed in. if null passed in, returns first model
    Example::findOne(QueryParameters?);

**Note: These helper methods do not apply to the `AssociativeModel` class.**

<a name="query-parameters"></a>
#### Query Parameters

The `QueryParameters` object gives you a way to easily define the criteria to use during data retrieval. You can define certain criteria like sort order, limit of results to retrieve, column equality checks, and more. The list below shows all the important methods you have access to.

| Method | Description | Example |
| --- | --- | --- |
| between | returns records with a column that has values between two values | `between('likes', 1, 10)` |
| betweenDates | returns records with a column that has values between two values | `betweenDates('date_created', '2019-01-01', 2019-01-31')` |
| equals | checks for columns that equal a certain value | `equals('name', 'Intersect')` |
| in | checks for columns that are apart of a certain data array | `in('id', [1, 3, 5])` |
| isNull | checks for columns that are NULL | `isNull('name')` |
| isNotNull | checks for columns that are not NULL | `isNotNull('name')` |
| like | checks for columns that are similar to a value | `like('name', 'Intersect%')` |
| notEquals | checks for columns that do not equal a certain value | `notEquals('name', 'Intersect')` |
| setColumns | columns to be returned in the response | `setColumns(['id', 'name'])` |
| setLimit | the number of records to be returned in the response | `setLimit(5)` |

Here are some example usages of `Model` retrieval using `QueryParameters`. Note that not all available methods are shown in the example, but the usage is still similar for each scenario.

    <?php

    use App\Example;
    use Intersect\Database\Query\QueryParameters;

    // find all models where description equal to "Intersect"
    $queryParameters = new QueryParameters();
    $queryParameters->equals('description', 'Intersect');
    $results = Example::find($queryParameters);

    // find all models where ids are 1, 3, and 9
    $queryParameters = new QueryParameters();
    $queryParameters->in('id', [1, 3, 9]);
    $results = Example::find($queryParameters);

    // find the first five models where description is NULL 
    // order by the ids in ascending order
    $queryParameters = new QueryParameters();
    $queryParameters->isNull('description');
    $queryParameters->setLimit(5);
    $queryParameters->setOrder('description ASC'); // ASC or DESC
    $results = Example::find($queryParameters);

<a name="updating-data"></a>
### Updating Data

Using the `Example` model from [above](#example-model), this is how you would go about updating an existing `Example` model record in the database. This example assumes you have a table with an "*id*" primary key column and a "*description*" column.

The response of `$updatedExample` should be a fully populated `Example` model object containing all updated attribute data.

    <?php

    use App\Example;

    $example = Example::findById(1); // assumes record with "id" of "1" exists
    $example->description = 'This is an updated description';
    $updatedExample = $example->save();

    echo $updatedExample->description; // This is an updated description

Note: Models are only updated if the primary key property value is set. In the above case, the `id` property is populated from the existing record during retrieval performed in the `findById` method call.

<a name="deleting-data"></a>
### Deleting Data

Using the `Example` model from [above](#example-model), this is how you would go about deleting an existing `Example` model record in the database. This example assumes you have a table with an "*id*" primary key column and a "*description*" column.

The response of `$deleted` should be a fully populated `Example` model object containing all updated attribute data.

    <?php

    use App\Example;

    $example = Example::findById(1); // assumes record with "id" of "1" exists
    
    $deleted = $example->delete(); // true / false

Note: Models are only delete if the primary key property value is set. In the above case, the `id` property is populated from the existing record during retrieval performed in the `findById` method call. For the cases where no primary key property value is set, the result will be `false` and no attempt to delete will be made.

<a name="other-model-types"></a>
## Other Model Types

Besides the normal `Model` class to extend from, there are a few other classes that provide some additional features onto of the normal `Model` class.

| Model Class | Description | 
| --- | --- |
| AssociativeModel | used for two column association tables |
| ReadOnlyModel | does not allow creating, updating, or deleting of a model |
| TemporalModel | provides automatic population for created/update timestamp columns |

<a name="associative-model"></a>
### AssociativeModel

The `AssociativeModel` is a special type of model. The model is used whenever you have a database table that you intend to use as an association table or link table. Normally these types of tables are used to join two other tables together by the associated primary key values stored in the association table.

You will still use the `save` and `delete` instance methods the way you normally would for other models. The real differences are the requirements for how you define your model class and the helper methods for retrieving data.

#### Defining Model

Extend your model from the `AssociativeModel` and implement the two required methods named `getColumnOneName` and `getColumnTwoName`.

The following example assumes you have a table with columns named `video_id` and `video_details_id`.

    <?php

    namespace App;

    use Intersect\Database\Model\AssociativeModel;

    class Example extends AssociativeModel {

        protected function getColumnOneName()
        {
            return 'video_id';
        }

        protected function getColumnTwoName()
        {
            return 'video_details_id';
        }

    }

#### Helper Methods

The following helper methods can be used to retrieve data from this association table.

    // returns model with `video_id` column value of "1" and `video_details_id` column value of "2"
    Example::findAssociation(1, 2);

    // returns array of models where `video_id` column value is "1"
    Example::findAssociationsForColumnOne(1);

    // returns array of models where `video_details_id` column value is "2"
    Example::findAssociationsForColumnTwo(2);

<a name="readonly-model"></a>
### ReadOnlyModel

Defining your model as a `ReadOnlyModel` means that you are not allowed to create, update, or delete a record using this model. This model is useful for when you have a pre-populated database table that you only want data to be read from.

If you attempt to invoke either the `save` or `delete` methods, a `Intersect\Database\Exception\OperationNotSupportedException` will be thrown.

    <?php

    namespace App;

    use Intersect\Database\Model\ReadOnlyModel;

    class Example extends ReadOnlyModel {

    }

<a name="temporal-model"></a>
### TemporalModel

Defining your model as a `TemporalModel` means that you will automatically get created and updated date columns populated upon invoking the `save` method. By default, the column names are assumed to be named `date_created` and `date_updated`.

If you are using columns other than the two default columns, you can override the `$dateCreatedColumn` and `$dateUpdatedColumn` properties. You can also set either of those properties to null if you only use one or the other and do not have those columns defined in your table.

    <?php

    namespace App;

    use Intersect\Database\Model\TemporalModel;

    class Example extends TemporalModel {

        // protected $dateCreatedColumn = 'date_created_override';
        // protected $dateUpdatedColumn = 'date_updated_override';
        
    }