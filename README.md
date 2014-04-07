WebSynergia developer's guides
======

Complete guide for programming, using Git and many more.

The content below describes the best practices and internal protocol we use in our projects.  Try to stick to the guidelines as much as possible to ensure consistency in code between all developers and applications.
 
Language explanation:
 
* “Use” means always applicable.
* “Prefer” means it’s better to use the way it’s described but if you have good reasons to do otherwise then fell free to do so.
* “Don’t” means never, “avoid” means it’s better to do it the other way but sometimes might be applied.
 
Git
-------

Some basic rules:
 
* Don’t include in the repo files containing passwords and other confidential data, like application.yml, database.yml, application’s secret key base etc.
* Prefer rebasing over merging from remote branches.
* Squash commits with trivial changes like “margin change in admin page”
* Get familiar with Gitlab (https://www.gitlab.com) as it’s our primary collaboration tool
 
Protocol
 
Staging is in most cases the default branch. Master is for production ready commits and hot fixes, don’t commit anything directly in master.
 
Start with pulling the recent changes on both staging and master:
 
```
git checkout master
git pull remote master
git checkout staging
git merge remote staging
```
 
And create a feature branch:
 
```
git checkout –b feature-branch-name
```
 
Remember about synchronizing often with remote branch:
 
```
git fetch remote
git rebase remote/staging
```
 
Some conflicts may happen, the more frequently you pull changes, the less problematic it will be. Prefer splitting big features into smaller chunks with frequent commits.  If you are done, stage and commit changes:
 
```
git add --all
git commit --a
```
 
Remember about running tests before commiting. We use Gitlab CI for continuous integration and after each commit the runner is running tests but just to keep things clean it's better to ensure all tests pass.
 
Provide descriptive commit message. Avoid short one-line statements like “User CRUD”, rather write a short summary, some longer description and maybe add a link to project management system where the feature was discussed:
 
```
Implement creating subtasks
 
This feature gives ability to create subtasks that belong to the parent task along with draggable interface – now it is easy to change the sequence of the tasks and assign them to the other parent task or project.
 
The feature was discussed at: basecamp/trello/whatever link.
```
 
Prefer present tense in summary over the past – it will be more consistent with Git’s convention (e.g. when reverting commits or merging branches).
 
Avoid emotional statements (“Damn, another fix in this feature, it’s buggy as hell”), they don’t provide much value.
 
Push your feature branch to Gitlab repo:
 
```
git push remote my-awesome-feature-branch
```
 
And ask other developer(s) for code review before merging. We use Gitlab specific workflow. Get familiar with some useful hooks, like special commit messages for solving issues. Gitlab created awesome tutorial about workflow: http://www.youtube.com/watch?v=raXvuwet78M, it provides most of the information needed when working with Gitlab.
 
Code reviews
-------
 
* Be polite
* Be very specific about the problem – it’s much more difficult to express your opinion online, don’t use sarcasms, ambiguous terms, etc.
* Don’t use words like: always, never, everything, nothing, no one etc.
* Avoid judging the difficulty and how fast something can be done. Don’t use terms like “easy change”, “trivial problem”, “obvious”, it’s just your personal opinion and it puts pressure on other developer
* Assume collective ownership of the code. Don’t use words like “mine”, “yours”, it is all “ours”
* Even if you are pretty convinced that you are right, still be humble (“Have you tried moving the logic from private methods to another class?” instead of “It must be moved to another class.”)
* Be constructive, always provide alternative solution if you disagree (“This implementation is half-assed, needs refactoring” is not constructive, better: “The methods in that class follow the similar pattern, how about some metaprogramming and using define_method to avoid repetition?”)
* Don’t take anything personally, the code is under review, not you
* Be thankful for an alternative solution, even if you don’t agree (“Thanks for the solution. However, …”)
* If you are reviewing code and it’s, give the thumbs up
* If you can’t agree on a matter, involve another developer in code review
 
 
Technology
-------
 
Our primary technology stack consists of:
 
* MRI Ruby / Ruby on Rails (main language and web framework)
* RSpec (Ruby testing framework)
* Jasmine (Javascript testing framework)
* Chef (servers’ setup automation tool)
* PostgreSQL (main database management system)
* Javascript (Coffeescript) / jQuery / AngularJS
* Nginx / Passenger (http / application servers)
* (When necessary) JRuby / Torquebox
* Redis
* (Sometimes)MongoDB
 
RSpec is powerful testing framework and provides great readability – the tests are extremely expressive. Testing is one of the most important developer’s responsibilities and we need the best possible tools.
 
We like symmetry – so we chose Jasmine for testing Javascript code, which is quite similar to RSpec.
 
PostgreSQL is our main db choice. Since 9.1 version, thanks to hstore and JSON data types, NoSQL-like functionality is available and it makes Postgres reasonable choice for most web applications.
 
We use Redis for background jobs (with Sidekiq) if needed and to enhance performance and MongoDB for very specific type of functionality where the Postgres might not be the most efficient solution (e.g. for statistics engine when very fast writing is required).
 
Coffeescript offers some significant improvements over plain Javascript, even if it’s only syntactic sugar. However, some developers are not really comfortable with Coffee and prefer JS. Depending on a project, the choice may vary, but in general, Coffeescript is preferred for apps with complex frontend.
 
For highly interactive web applications we use AngularJS as JS MVC framework. We appreciate Angular’s approach to testing, maturity, high level of productivity and the joy of development, which makes it our primary choice for Javascript framework.
 
We prefer Phussion Passenger for it’s stability, ease of use and performance as an application server.
 
When needed (big applications under heavy traffic), we switch from MRI Ruby to JRuby and use Torquebox as an application server to ensure the best possible performance.
  
Style
-------
 
Formatting
 
* Avoid using spaces after (, [ and before ), ]
* Delete trailing whitespaces
* Use an empty line between methods and class declaration and rest of the class body
* Use two space indentation
* Use space after commas, { }
* For multiline hashes, write one key-value pair per line:
{
   name: “John”,
   surname: “Smith”
}
* For multiline arrays, apply the following format:
[
  Start here
]
* When chaining multiple method calls and breaking it up, keep one method invocation per line and use dot at the beginning of the line:
 
``` ruby

first_method
  .second_method
  .another_method 
```
 
Ruby
 
* Avoid semicolons
* Avoid ternary operators – prefer multiline conditionals
* Don’t use explicit returns unless necessary (like early return)
* Avoid semicolons
* Write bang (!) methods only if they raise exception(s)
* Don’t use explicit self except defining class-level methods and assigning attributes
* Prefer writing class methods like def self.class_method, not within class << self
* Avoid parentheses for functions/methods without arguments (exception: super(), which calls method without arguments in that case)
* Prefer {} syntax for one-liners and do..end for multiline blocks.
* Avoid using for loop for iterations – use each
* For methods and variables use snake_case, CamelCase for modules and classes and UPPERCASED_CAMEL_CASE  for constants
* Prefer question mark (?) for methods’ names returning boolean over containing is (valid? over is_valid)
* Make methods private unless it needs to be public
* Prever new Ruby (since 1.9) hash syntax over hash rockets
* Avoid using instance variables directly – use attribute readers / writers and make these methods private unless it is necessary to make them public, e.g.
 
``` ruby
attr_reader :user
private :user
 
def initialize(user)
  @user = user
end
```
 
* Prefer one-line declarations when using class-level macros over multiline declarations, e.g.

``` ruby
# prefer this one
attr_reader :user, :article
 
#over this one
attr_reader :user
attr_reader :article
``` 
 
* Prefer composition over inheritance
* When using inheritance, provide interface so that you won’t call #super in the constructor or use constructor at all in subclasses, example by providing hook methods like #post_initialize()
 
``` ruby
 
class SomeClass
 
  attr_reader :user
  private :user
 
  # provide possibility for some extensions – like options hash argument
  def initialize(user, options={})
    @user = user
    post_initialize(options)
  end
 
  def post_initiliaze(options={})
    nil
  end
 
end
 
 
class AnotherClass < SomeClass
 
   def post_initialize(options={})
      @country = options.fetch(:country, “Poland”)  
   end
 
end
```
 
* When passing options as argument, always use Hash#fetch method (even if you expect something to be nil, avoid #[], make  nil the default value instead)
* Prefer dependency injection over directly referencing class and/or instantiating objects on your own
* Prefer private methods over protected
* Prefer #detect method over #find (in Rails apps)
* Prefer #reduce method in one-liners, #inject in multiline methods
* Prefer #map over #collect
* Use descriptive variable names and methods
* Before writing comment, consider extracting it to private method with descriptive name
* Apply naming scope: public methods / classes should have short names, private ones can have long and descriptive names, variables with long scope can have a long name, with short scope (like index in #each_with_index) can be short
* Avoid using meaningless words in names, like Service, Manager (UserRegistration vs. UserRegistrationService vs UserRegistrationManager) unless the convention tells otherwise
* For array of strings, prefer %w[] syntax over plain array
* Don’t use “magic” parameters – instead of using e.g. 3600 directly, extract it to a private method / constant with descriptive name, like ONE_HOUR_IN_SECONDS
* Inherit from StandardError when defining new exceptions
* Avoid: rescue Exception => e, refer directly the exceptions you want to rescue from
* Avoid several lines of code within begin..rescue block as it makes it harder to reason about, prefer one line where you expect the exception to happen
* Avoid class variables (@@) unless you exactly know why it must be that way (class variables are like globals within classes and can be modified in subclasses) - prever class instance variable


Rails
-------
 
* When creating new app, use our Rails App Creation Kit
* Use Guard and Spork when testing
* Avoid using #try method, use delegate macro from ActiveSupport instead
* Avoid conditional validations and callbacks
* Limit application of ActiveRecord callbacks to: data formatting (like removing whitespaces before persistence), parameterizing (e.g. creating parameterized_name from name) and statistics calculations (number of records in has_many associations like houses amount in investments or prices range in investment’s apartments) purposes or some database-specific actions (like refreshing materialized views), the use case / service object is probably a better solution
* In ActiveRecord models use validations that are always applicable to the model (like presence validation for columns with null: false constraints, uniqueness validations for columns with unique index, inclusion validation for enum fields), for other purposes form object might be a better idea
* Create domain methods around ActiveRecord API (e.g. instead of calling post.update(spam: true) create method: flag_as_spam with update(spam: true) method body, create class methods for ActiveRecord query interface), try not to use ActiveRecord API outside models (using #save, #destroy, #update with mass assignment is fine)
* Use transactions when persisting multiple models
* Consider other possibilities before using STI
* Prefer Wiselinks over Turbolinks
* Order stuff alphabetically in: ActiveRecord relations, validations, factories, i18n translations
* Avoid setting instance variables in controllers in before_filters, even when it is not really DRY, instead of calling method as before_filter, do it directly in the function body
* Prefer using decent_exposure gem and/or explicitly render views with locals (might be an overkill for small application)
* Avoid using models (classes) directly in views
* Avoid using gems that provide complete features - devise is perfectly fine, user registration / authentication is more like an infrastructure for the app, however, when implementing e.g. activity feeds, it might be better to spend some time on writing own, more flexible solution than using public_activity gem)
* ActiveRecord models should be responsible for: always applicable validations (in most cases reflecting the database schema with inclusion validation), handling relationships, own state, providing interface by macros from external gems. Can be used as form object as long as it doesn’t require adding any other methods like virtual attributes (accepts_nested_attributes_for is fine for one-to-many relations, expecially with nested_form gem, like adding multiple photos, especially for one-to-one use dedicated form object (e.g. creating user with profile))
* Use dedicated form objects (with Reform gem or your own with Virtus gem and ActiveModel interface) when multiple models are involved, custom validations or virtual attributes (app/forms directory)
* Simple persistence (like saving two models) can be done in form object (create #persist method), otherwise prefer service objects
* Extract business logic to service objects (app/usecases directory, important: don’t use app/services directory as it collides with Torquebox services convention)
* Extract background jobs to separate objects (app/jobs)
* Use presenters for presentation logic (draper gem, app/decorators directory)
* Prefer cells over partials (app/cells directory)
* Explicitly call partials with locals (limit partials’ applications to rendering some portion of data when making partial request with Wiselinks, in other cases cells are probably a better solution)
* Extract policies to separate objects (app/policies) – can be called within models
* When creating API use ActiveModel Serializers (app/serializers)
* For uploading and image processing use uploaders (carrierwave gem + mini_magick gem, app/uploaders directory)
* Use helpers only for non-models related stuff (in most cases it is only used for some HTML markup)
* Use Delayed Job for simple cases, when better performance is needed use Sidekiq to handle background jobs
* Use figaro gem for handling passwords, app’s secret token and other confidential data
* Provide example files for config (database.yml.example, application.yml.example)
* Add some app-specific actions needed to run to Readme (like custom rake tasks)
* Get Errbit API key for error tracking 
* More about separation of concerns, structuring app etc. : http://karolgalanciak.com/blog/2013/10/06/structuring-rails-applications/ 
* Prefer if ! over unless
* Don’t use unless...else
* Don’t use double negations (unless .blank?)

Testing
-------
 
* Use the following stack: RSpec + Guard + Spork (MRI Ruby only) + Capybara + Poltergeist + Database Cleaner + FactoryGirl (Ruby), Jasmine (Javascript)
* Avoid using instance variables in tests, use let and subject instead
* Use feature/scenario methods from Capybara
* Use one factories.rb file per project
* Prefer expect syntax over should
* Prefer not_to over to_not
* When testing instance methods, use describe “#instance_method” style, for testing class methods use describe “.class_method”
* Use context for providing background when more than one scenario is possible
* Use should syntax for one-liners (https://github.com/rspec/rspec-expectations/blob/master/Should.md#one-liners)
* Put Acceptance tests in spec/features
* Avoid tests that don’t give much value (more: http://karolgalanciak.com/blog/2014/01/04/test-driven-rails-part-1/)
* Apply Test-Driven Development cycle: write tests first, write a minimal implementation to satisfy that test and then write more code to satisfy another tests. Refactor at the end.
* Start with acceptance tests with Capybara by testing feature from the user’s perspective, then move to the unit tests
* More about testing Rails apps: http://karolgalanciak.com/blog/2014/03/03/test-driven-rails-part-2/
 
SQL
-------
 
* Prefer multiple tables over arrays
* Use null: false constraints when needed
* Add foreign keys constraints when necessary (with schema_plus gem)
* Uniqueness validation does not guarantee uniqueness, use unique index for ensuring uniqueness besides proper validation
* For boolean fields, use null: false constraint and provide default value (either true or false to avoid null value)
* If STI is the best possible solution, use hstore for model-specific fields
* Write keywords in upper case and names in lower case (http://www.postgresql.org/docs/9.2/static/sql-syntax-lexical.html#SQL-SYNTAX-IDENTIFIERS)
* Use partial indexes and index specific queries when possible
* Investigate complex queries with EXPLAIN (ANALYZE, BUFFERS) and seek for optimization possibilities
* Use materialized views (http://www.postgresql.org/docs/9.3/static/sql-creatematerializedview.html) for complex queries optimization involving multiple tables and joins.
 
 
 
Javascript / Coffesscript
-------
 
* Javascript – prefer === and !== operators over == and !=
* Coffee – prefer is over ==
* For methods and variables names use firstLetterSmallCamelCase, CamelCase for classes and UPPERCASED_CAMEL_CASE  for constants
* Use underscore (_method) at the beginning for marking methods private
 
 
License
-------

Guides © 2014 WebSynergia, inc.

The content is distributed under the [Creative Commons
Attribution License](http://creativecommons.org/licenses/by/3.0/).
 






