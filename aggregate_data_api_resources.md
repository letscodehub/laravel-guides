
## Example of the parent and child aggregate data in laravel by using the belongTo and hasMany methods and apiResources classes.
- Table a: Student
- Table b: Course

## NOTE: This is not a complete project, I wrote below snippets to get you started on implementing aggregate json data. Apply the technique, and do your research.

## Database->Migrations folder (sample codes):


### Student
***

```
Schema::create(‘students’, function (Blueprint $table) {
            $table->uuid('uid')->unique()->index();
            $table->string('fname');
            $table->string('lname');
            $table->string('email')->unique();
});
```

### Course
***
```
Schema::create('student_course)', function (Blueprint $table) {
            $table->bigIncrements('id')->unsigned()->unique();
            $table->string('student_uid')->index();
            $table->string('name');
            $table->unique(['student_uid', 'name']);

            $table->foreign('student_uid')->references('uid')->on(‘students’);
 });
 ```

## App->Models folder (sample codes):

### Student
***
```
use App\Models\Course;
class Student extends Authenticatable
{
    use HasFactory;
    protected $fillable = [“fname”, “lname”, “email”]
    protected $primaryKey = 'uid';
    protected $table = ‘students’;

    public function course()
    {
        return $this->hasMany(Course::class);
    }
}
```

### Course
***
```
use App\Models\Student;
class Course extends Authenticatable
{
    use HasFactory;
    protected $guarded = [];
    public $cast = [
        ‘name’ => ‘string’
    ];
    public function student() : BelongsTo
    {
        return $this->belongsTo(Student::class);
    }
}
```

## app->Http->Controller folder (sample codes):

### Student
***
```
use Illuminate\Http\Response;
use App\Http\Resources\StudentCollection;

class StudentController extends ApiController
{
   public function index()
    {
        $obj = Student::where('uid', '<>', auth()->student()->uid));
        return response()->json(
            new StudentCollection(
                $obj
            ), Response::HTTP_OK);
    }
}
```

## app->Http->Resources folder (sample codes):

### Student
***
```
use Illuminate\Http\Resources\Json\JsonResource;
class StudentResource extends JsonResource
{
  public function toArray($request)
    {
      'ID' => $this->uid,
      ‘FirstName’=> $this->fname,
      ‘LastName’=> $this->lname,
      ‘CoursesInfo’=> $this->student_courses
}
```

## Routes->api (sample codes):
```
use App\Http\Controllers\StudentController;
use App\Http\Controllers\CourseController;

Route::apiResource('/display_students’, StudentController::class)
```

## API sample outputs (/display_students):
```
{
  "id": "12345",
  "FirstName": "Mary",
  "LastName": "Jane",
  "CourseInfo" : [
              {
                  "id": "7890"
                  "name": "Computer Science"
              },
              {
                  "id": "06987"
                  "name": "Statistics"
              }
  ]
}
```


