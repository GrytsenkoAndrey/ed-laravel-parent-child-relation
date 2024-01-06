# ed-laravel-parent-child-relation
Using Parent-Child Recursive Relationships in Laravel

Recursive relationships, especially in the context of data like category hierarchies, are quite common in many applications. Laravel, a popular PHP framework, offers robust support for managing these kinds of relationships. This article will explore how to utilize a parent-child recursive relationship in Laravel using the example of category data.

## What is a Parent-Child Recursive Relationship?

In databases, a parent-child recursive relationship occurs when a record in a table is related to another record in the same table. In the context of categories, this means a category can have a parent that’s also a category. This creates a hierarchical structure, making it possible to have categories, sub-categories, sub-sub-categories, and so on.

## Setting Up The Database

To demonstrate, we’ll create a `categories` table:

```
Schema::create('categories', function (Blueprint $table) {
    $table->id();
    $table->unsignedBigInteger('parent_id')->nullable();
    $table->string('name');
    $table->timestamps();

    $table->foreign('parent_id')
        ->references('id')
        ->on('categories')
         ->onDelete('cascade');
});
```

Here, the parent_id column refers to the parent category. If it’s null, the category is a top-level category.

## Defining The Eloquent Relationship

Next, we’ll define the Eloquent relationships in our Category model:

```
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Category extends Model
{
    use HasFactory;

    protected $fillable = [
        'name',
        'parent_id'
    ];

    public function parent()
    {
        return $this->belongsTo(Category::class, 'parent_id');
    }

    public function children()
    {
        return $this->hasMany(Category::class, 'parent_id');
    }

    public function descendants()
    {
        return $this->children()->with('descendants');
    }
}
```

- parent() relationship gives the immediate parent category.
- children() provides the direct subcategories.
- descendants() gives all the child categories recursively.
