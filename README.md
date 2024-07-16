<!DOCTYPE html>
<html>
<head>
</head>
<body>
<h1>Optimize Laravel Database Queries</h1>

<h4>Select only the columns you need</h4>
<pre>
	$posts = Post::find(1);
	$posts = DB::table('posts')->where('id','=',1)->first();

	$posts = Post::select(['id','title'])->find(1);
	$posts = DB::table('posts')->where('id','=',1)->select(['id','title'])->first();
	$users = User::select(['id', 'name'])->get();
</pre>

<h4>Retrieving large datasets</h4>
<pre>
	1 chunk

	$posts = Post::chunk(100, function($posts){
		foreach ($posts as $post){
		}
	});

	2 cursor
	foreach (Post::cursor() as $post){
	}

	3 chunkById
	$posts = Post::chunkById(100, function($posts){
		foreach ($posts as $post){
		}
	});
</pre>

<h4>Use eager loading</h4>
<pre>
	$posts = Post::with('comments')->get();
	$posts = Post::with(['author'])->get();
	$posts = Post::with(['author.team'])->get();
</pre>

<h4>Caching mechanisms</h4>
<pre>

	use Illuminate\Support\Facades\Cache;

	$users = Cache::remember('users', 10, function () {
		return DB::table('users')->get();
	});

	$users = Cache::remember('users', Carbon::now()->addMinutes(10), function () {
		return User::select('id', 'name')->get();
	});
</pre>

<h4>Pluck the field you need</h4>
<pre>
	$user = User::query()->pluck('name');
	$user = User::query()->pluck('id','name');
</pre>

<h4>Creating indexess</h4>
<pre>
	$table->string('name')->index();
	$table->index('name');
	$table->index(['first_name', 'last_name']);
</pre>
<h4>Where like searches and indexes</h4>
<pre> 
	$users = User::query()->where('name', 'like', "{$search}%")->get();
</pre> 

<h4>Count rows using query instead of collection </h4>
<pre> 
	$posts = Post::all()->count();
	$posts = DB::table('posts')->get()->count();

	$posts = Post::count();
	$posts = DB::table('posts')->count();
</pre> 

<h4>Models - Mass assignment</h4>
<pre>
	protected $guarded = []; // Empty means all attributes are guarded

	protected $fillable = ['name', 'email'];

	protected $hidden = ['password', 'remember_token'];

	protected $casts = [
		'email_verified_at' => 'datetime',
	];

	protected $dates = ['deleted_at'];

	protected $with = ['posts'];

	protected $withCount = ['posts'];

	protected $appends = ['full_name'];
</pre>

<h4>Merge similar queries together</h4>
<pre> 
	$publishedPosts = Post::where('status','=','published')->get();
	$featuredPosts = Post::where('status','=','featured')->get();
	$scheduledPosts = Post::where('status','=','scheduled')->get();

	$posts =  Post::whereIn('status',['published', 'featured', 'scheduled'])->get();
	$publishedPosts = $posts->where('status','=','published');
	$featuredPosts = $posts->where('status','=','featured');
	$scheduledPosts = $posts->where('status','=','scheduled');
</pre> 


<h4>Use simplePaginate instead of Paginate</h4>
<pre>
	If your database table has so many rows, it is better to avoid paginate and do simplePaginate instead.

	Generates pagination links for all the pages
	$posts = Post::paginate(20); 

	Generates only next and previous pagination links
	$posts = Post::simplePaginate(20);
</pre>
<h4>Avoid using SQL functions in where clause</h4>
<pre> 
	$posts = POST::whereDate('created_at', '>=', now() )->get();

	$posts = Post::where('created_at', '>=', now() )->get();
</pre> 

<h4>Better way to retrieve latest rows</h4>
<pre> 
	$posts = Post::latest()->get();
</pre> 
</body>
</html>
