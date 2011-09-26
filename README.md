# Prelude

### Bozhidar Batsov _(from the [upstream original][upstream])_:
> One thing has always bothered me as Ruby developer&mdash;Python devs have a great programming
> style reference (PEP-8), and we never got an official guide documenting Ruby coding style and best
> practices. And I do believe that style matters.
>
> This document was originally created when I, as the Technical Lead of the company which I work
> for, was asked by our CTO to create some internal documents describing good style and best
> practices for Ruby programming. I started off by building upon
> [this existing style guide][original], since I concurred with many of the points in it. At some
> point I decided that the work I was doing might be interesting to members of the Ruby community in
> general and that the world had little need of another internal company guideline. But the world
> could certainly benefit from a community-driven and community-sanctioned set of practices, idioms
> and style prescriptions for Ruby programming.
>
> Since the inception of the guide I've received a lot of feedback from members of the exceptional
> Ruby community around the world. Thanks for all the suggestions and the support! Together we can
> make a resource beneficial to each and every Ruby developer out there.

[original]: https://github.com/chneukirchen/styleguide

By the way, if you're into Rails you might want to check out the
complementary
[Ruby on Rails 3 Style Guide](https://github.com/rtlong/rails-style-guide).

## Table of Contents

* [The Ruby Style Guide](#guide)
    * [Source Code Layout](#layout)
    * [Syntax](#syntax)
    * [Naming](#naming)
    * [Comments](#comments)
    * [Annotations](#annotations)
    * [Classes](#classes)
    * [Exceptions](#exceptions)
    * [Collections](#collections)
    * [Strings](#strings)
    * [Percent Literals](#literals)
    * [Miscellaneous](#misc)
    * [Design](#design)
* [Contributing](#contributing)
* [Spread the word](#spreadtheword)

# The Ruby Style Guide

This Ruby style guide recommends best practices so that real-world Ruby
programmers can write code that can be maintained by other real-world Ruby
programmers. A style guide that reflects real-world usage gets used, and a
style guide that holds to an ideal that has been rejected by the people it is
supposed to help risks not getting used at all &ndash; no matter how good it is.

The guide is separated into several sections of related rules. I've
tried to add the rationale behind the rules (if it's omitted I've
assumed that is pretty obvious).

I didn't come up with all the rules out of nowhere - they are mostly
based on my extensive career as a professional software engineer,
feedback and suggestions from members of the Ruby community and
various highly regarded Ruby programming resources, such as
["Programming Ruby 1.9"](http://pragprog.com/book/ruby3/programming-ruby-1-9)
and ["The Ruby Programming Language"](http://www.amazon.com/Ruby-Programming-Language-David-Flanagan/dp/0596516177).

The guide is still a work in progress - some rules are lacking
examples, some rules don't have examples that illustrate them clearly
enough. In due time these issues will be addressed - just keep them in
mind for now.

You can generate a PDF or an HTML copy of this guide using
[Transmuter](https://github.com/TechnoGate/transmuter).

<a name="layout">
## Source Code Layout

> Nearly everybody is convinced that every style but their own is
> ugly and unreadable. Leave out the "but their own" and they're
> probably right... <br/>
> -- Jerry Coffin (on indentation)

* Use `UTF-8` as the source file encoding.
* Use two **spaces** per indentation level.

    ```Ruby
    # good
    def some_method
      do_something
    end

    # bad - four spaces
    def some_method
        do_something
    end
    ```

* Use Unix-style line endings. (*BSD/Solaris/Linux/OSX users are covered by default,
  Windows users have to be extra careful.)
    * If you're using Git you might want to add the following
    configuration setting to protect your project from Windows line
    endings creeping in:

        ```
        $ git config --global core.autocrlf true
        ```

* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`. Whitespace might be (mostly) irrelevant to the Ruby
  interpreter, but its proper use is the key to writing easily
  readable code.

    ```Ruby
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts 'Hi'
    [1, 2, 3].each { |e| puts e }
    ```

    The only exception is when using the exponent operator:

    ```Ruby
    # bad
    e = M * c ** 2

    # good
    e = M * c**2
    ```

* No spaces after `(`, `[` or before `]`, `)`.

    ```Ruby
    some(arg).other
    [1, 2, 3].length
    ```

* Indent `when` as deep as `case`. I know that many would disagree
  with this one, but it's the style established in both the "The Ruby
  Programming Language" and "Programming Ruby".

    ```Ruby
    case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts %q(It's too late)
    else
      song.play
    end

    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           when 1910..1929 then 'New Orleans Jazz'
           when 1930..1939 then 'Swing'
           when 1940..1950 then 'Bebop'
           else 'Jazz'
           end
    ```

* Use empty lines between `def`s and to break up a method into logical
  paragraphs.

    ```Ruby
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
      result
    end
    ```

* Use RDoc and its conventions for API documentation.  Don't put an
  empty line between the comment block and the `def`.
* Keep lines fewer than 100 characters.
* Avoid trailing whitespace.
    * Textmate users might want to take a look at the [Uber Glory bundle][uber_glory].

[uber_glory]: https://github.com/glennr/uber-glory-tmbundle

<a name="syntax"/>
## Syntax

* Use `def` with parentheses when there are arguments. Omit the parentheses when the method doesn't
  accept any arguments.

     ```Ruby
     def some_method
       # body omitted
     end

     def some_method_with_arguments(arg1, arg2)
       # body omitted
     end
     ```

* Never use `for`, unless you know exactly why. Most of the time iterators
  should be used instead. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.

    ```Ruby
    arr = [1, 2, 3]

    # bad
    for elem in arr do
      puts elem
    end

    # good
    arr.each { |elem| puts elem }
    ```

* Never use `then` for multi-line `if/unless`.

    ```Ruby
    # bad
    if some_condition then
      # body omitted
    end

    # good
    if some_condition
      # body omitted
    end
    ```

* Favor the ternary operator(`?:`) over `if/then/else/end` constructs.
  It's more common and obviously more concise.

    ```Ruby
    # bad
    result = if some_condition then something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

    ```Ruby
    # bad
    some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

    # good
    if some_condition
      nested_condition ? nested_something : nested_something_else
    else
      something_else
    end
    ```

* Never use `if x: ...` &mdash; it is removed in Ruby 1.9. Use the ternary operator instead.

    ```Ruby
    # bad
    result = if some_condition: something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

* Never use `if x; ...`. Use the ternary operator instead.
* Avoid multi-line `x ? a : b` (the ternary operator), use `if/unless` instead.
* Use `when x then ...` for one-line cases. The alternative syntax `when x: ...` is removed in
  Ruby 1.9. The same is true for the `when x; ...` syntax.
* Use `&&` and `||` for boolean expressions, `and` and `or` for control flow. (Rule of thumb: if you
  have to use outer parentheses, you are using the wrong operators.)

    ```Ruby
    # boolean expression
    if some_condition && some_other_condition
      do_something
    end

    # control flow
    document.saved? or document.save!
    ```

* Avoid multi-line `?:` (the ternary operator), use `if/unless` instead.

* Favor modifier `if/unless` usage when you have a single-line
  body. Another good alternative is the usage of control flow `and/or`.

    ```Ruby
    # bad
    if some_condition
      do_something
    end

    # good
    do_something if some_condition

    # another good option:
    some_condition and do_something
    ```

* Favor `unless` over `if` for negative conditions (or control flow `or`).

    ```Ruby
    # bad
    do_something if !some_condition

    # good
    do_something unless some_condition

    # another good option:
    some_condition or do_something
    ```

* Never use `unless` with `else`. Rewrite these with the positive case first.

    ```Ruby
    # bad
    unless success?
      puts 'failure'
    else
      puts 'success'
    end

    # good
    if success?
      puts 'success'
    else
      puts 'failure'
    end
    ```

* Don't use parentheses around the condition of an `if/unless/while`.

    ```Ruby
    # bad
    if (x > 10)
      # body omitted
    end

    # good
    if x > 10
      # body omitted
    end
    ```

* Omit parentheses around parameters for methods where not required. Use parentheses around the
  arguments when utilizing the return value or when chaining method invocations.

    ```Ruby
    class Person
      attr_reader name, age
    end

    temperance = Person.new('Temperance', 30)
    temperance.name

    puts temperance.age

    x = Math.sin(y)
    array.delete e
    ```

* Prefer `{...}` over `do...end` for single-line blocks.  Avoid using
  `{...}` for multi-line blocks (multiline chaining is always
  ugly). Always use `do...end` for "control flow" and "method
  definitions" (e.g. in Rakefiles and certain DSLs).  Avoid `do...end`
  when chaining.

    ```Ruby
    names = ["Bozhidar", "Steve", "Sarah"]

    # good
    names.each { |name| puts name }

    # bad
    names.each do |name|
      puts name
    end

    # good
    names.select { |name| name.start_with?("S") }.map { |name| name.upcase }

    # bad
    names.select do |name|
      name.start_with?("S")
    end.map { |name| name.upcase }
    ```

    Some will argue that multiline chaining would look OK with the use of {...}, but they should
    ask themselves - it this code really readable and can't the blocks contents be extracted into
    nifty methods.

* Use `return` freely where it is useful for clarity.

* Use spaces around the `=` operator when assigning default values to method parameters:

    ```Ruby
    # bad
    def some_method(arg1=:default, arg2=nil, arg3=[])
      # do something...
    end

    # good
    def some_method(arg1 = :default, arg2 = nil, arg3 = [])
      # do something...
    end
    ```

    While several Ruby books suggest the first style, the second is much more prominent
    in practice (and arguably a bit more readable).

* Avoid line continuation (\\) where not required. In practice, avoid using
  line continuations at all.

    ```Ruby
    # bad
    result = 1 - \
             2

    # only slightly better (but still ugly as hell)
    result = 1 \
             - 2
    ```

* Using the return value of `=` (an assignment) is ok.

    ```Ruby
    if v = array.grep(/foo/) ...
    ```

* Use `||=` freely to initialize variables.

    ```Ruby
    # set name to Bozhidar, only if it's nil or false
    name ||= 'Bozhidar'
    ```

* Avoid using Perl-style special variables (like `$0-9`, `$``,
  etc. ). They are quite cryptic and their use in anything but
  one-liner scripts is discouraged.

* Never put a space between a method name and the opening parenthesis.

    ```Ruby
    # bad
    puts (3 + 2) + 1

    # good
    puts(3 + 2) + 1
    ```

* If the first argument to a method begins with an open parenthesis, always use parentheses in the
  method invocation.

  ```Ruby
  puts((3 + 2) + 1)
  ```

* Always run the Ruby interpreter with the `-w` option so it will warn you if you forget either of
  the rules above!

<a name="naming"/>
## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* Use `snake_case` for methods and variables.
* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)
* Use `SCREAMING_SNAKE_CASE` for other constants.
* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`).
* The names of potentially "dangerous" methods (i.e. methods that modify `self` or the
  arguments, `exit!`, etc.) should end with an exclamation mark.
* When using `inject` with short blocks, name the arguments `|a, e|`
  (accumulator, element).
* When defining binary operators, name the argument `other`.

    ```Ruby
    def +(other)
      # body omitted
    end
    ```

* Prefer `collect` over `map`; `find` over `detect`; `select` over `find_all`; and `size` over
  `length`. This is not a hard requirement; if the use of the alias enhances readability, it's ok to
  use it.

<a name="comments"/>
## Comments

> Good code is its own best documentation. As you're about to add a
> comment, ask yourself, "How can I improve the code so that this
> comment isn't needed?" Improve the code and then document it to make
> it even clearer. <br/>
> -- Steve McConnell

* In comments written using `#`, `#` should be followed by a space.
* Comments longer than a word are capitalized and use proper punctuation and grammar.
  Use [one space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.
* Avoid superfluous comments.

    ```Ruby
    # bad
    counter += 1 # increments counter by one
    ```

* Keep existing comments up-to-date. No comment is better than an outdated comment.
* Avoid writing comments to explain bad code. Refactor the code to make it self-explanatory.

<a name="annotations"/>
## Annotations

* Annotations should usually be written on the line immediately above
  the relevant code.
* The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
* If multiple lines are required to describe the problem, subsequent
  lines should be indented two spaces after the `#`.

    ```Ruby
    def bar
      # FIXME: This has crashed occasionally since v3.2.1. It may
      #   be related to the BarBazUtil upgrade.
      baz :quux
    end
    ```

* In cases where the problem is so obvious that any documentation would be redundant, annotations
  may be left at the end of the offending line with no note. This usage should be the exception and
  not the rule.

    ```Ruby
    def bar
      sleep 100 # OPTIMIZE
    end
    ```

* Use `TODO` to note missing features or functionality that should be added at a later date.
* Use `FIXME` to note broken code that needs to be fixed.
* Use `OPTIMIZE` to note slow or inefficient code that may cause performance problems.
* Use `REFACTOR` to note code smells where questionable coding practices were used and should be
  refactored away.
* Use `REVIEW` to note anything that should be looked at to confirm it is working as intended. For
  example:

    ```Ruby
    # REVIEW: Are we sure this is how the client does X currently?
    ```

* Use other custom annotation keywords if it feels appropriate, but be sure to document them in the
  project's `README` file.

<a name="classes"/>
## Classes

* Excepting DSL classes (Rails models &amp; controllers, etc.), always supply a proper `#to_s`
  method.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def to_s
        "#@first_name #@last_name"
      end
    end
    ```

* Use the `attr` family of functions to define trivial accessors or
  mutators.
* Consider adding factory methods to provide additional sensible ways
  to create instances of a particular class.
* Prefer duck-typing over inheritance.
* Avoid the usage of class (`@@`) variables due to their "nasty" behavior
  in inheritance.
* Assign proper visibility levels to methods (`private`, `protected`)
  in accordance with their intended usage. Don't go off leaving
  everything `public` (which is the default). After all we're coding
  in *Ruby* now, not in *Python*.
* Indent the `public`, `protected`, and `private` methods as much the
  method definitions they apply to. Leave one blank line above them.

    ```Ruby
    class SomeClass
      def public_method
        # ...
      end

      private

      def private_method
        # ...
      end
    end
    ```

* Use `def self.method` to define singleton methods. This makes the methods
  more resistant to refactoring changes.

    ```Ruby
    class TestClass
      # bad
      def TestClass.some_method
        # ...
      end

      # good
      def self.some_other_method
        # ...
      end

      # Also possible and convenient when you have to define many singleton methods:
      class << self
        def first_method
          # ...
        end

        def second_method_etc
          # ...
        end
      end
    end
    ```

<a name="exceptions"/>
## Exceptions

* Never suppress exceptions.
* Don't use exceptions for flow of control.
* Avoid rescuing the `Exception` class.

<a name="collections"/>
## Collections

* It's ok to use arrays as sets for a small number of elements.
* Prefer `%w` to the literal array syntax when you need an array of
  strings.
* Avoid the creation of huge gaps in arrays.
* Use `Set` instead of `Array` when dealing with lots of elements.
* Use symbols instead of strings as hash keys.
* Avoid the use of mutable object as hash keys.
* Use the new 1.9 literal hash syntax in preference to the hashrocket syntax.
* Rely on the fact that hashes in 1.9 are ordered.
* Never modify a collection while traversing it.

<a name="strings"/>
## Strings

* Prefer string interpolation instead of string concatenation:

    ```Ruby
    # bad
    email_with_name = user.name + ' <' + user.email + '>'

    # good
    email_with_name = "#{user.name} <#{user.email}>"
    ```

* Prefer single-quoted strings when you don't need string interpolation or
  special symbols such as `\t`, `\n`, `'`, etc.

    ```Ruby
    # bad
    name = "Bozhidar"

    # good
    name = 'Bozhidar'
    ```

* Don't use `{}` around instance variables being interpolated into a
  string.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      # bad
      def to_s
        "#{@first_name} #{@last_name}"
      end

      # good
      def to_s
        "#@first_name #@last_name"
      end
    end
    ```

* Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string objects.

    ```Ruby
    # good and also fast
    html = ''
    html << '<h1>Page title</h1>'

    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    ```

* For multi-line strings, prefer [heredoc][].

    ```Ruby
    # Okay
    output =  'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec posuere, nisi nec'
    ouptut << 'molestie feugiat, libero lectus sollicitudin risus, vitae fermentum diam velit at'
    output << foo
    output << 'ante. Nullam vitae massa semper velit vestibulum scelerisque. Ut justo metus,'
    output << "dictum #{bar} non congue placerat, eleifend non felis. Pellentesque erat lectus."
    puts output


    # Better (avoids ever storing the variable; easier to read and work with)
    puts <<-OUTPUT.strip_heredoc
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec posuere, nisi nec molestie
      feugiat, libero lectus sollicitudin risus, vitae fermentum diam velit at #{foo} ante. Nullam
      vitae massa semper velit vestibulum scelerisque. Ut justo metus, dictum #{bar} non congue
      placerat, eleifend non felis. Pellentesque erat lectus, luctus sit amet.
    OUTPUT
    ```

[heredoc]: http://web.njit.edu/all_topics/Prog_Lang_Docs/html/ruby/syntax.html#here_doc

<a name="literals"/>
## Percent Literals

* Use `%w` or `%W` freely.

    ```Ruby
    STATES = %w(draft open closed)
    ```

* Use `%Q` or `%()` freely. Remember to use `%q` for cases that require no interpolation.

    ```Ruby
    %q(<div class="text">Some text</div>)
    %(This is #{quality} style)
    %Q(<div>\n<span class="big">#{exclamation}</span>\n</div>)
    %[<tr><td class="name">#{name}</td>]
    ```

* Avoid use of confusing delimiters with any of the `%` literals:

    ```Ruby
    %q"non-interpereted string"
    %'interepeted'
    %w/not a regex/
    ```

* Use `%r` for regular expressions matching `/` characters.

    ```Ruby
    # bad
    /^\/blog\/2011\/(.*)$/

    # good
    %r(^/blog/2011/(.*)$)
    ```

* Prefer `()`, `[]` or `{}` as delimiters for all `%` literals.

<a name="misc"/>
## Misc

* Write `ruby -w` safe code; that is, execute files with `ruby -w -c` to check syntax before
  committing.
* Avoid parameter lists longer than three or four parameters.
* If you really have to, add "global" methods to Kernel and make them private. Otherwise, never use
  global variables within Rails.
* Use class instance variables instead of global variables.

    ```Ruby
    #bad
    $foo_bar = 1

    #good
    class Foo
      class << self
        attr_accessor :bar
      end
    end

    Foo.bar = 1
    ```

* Avoid `alias` when `alias_method` will do.
* Use `OptionParser` for parsing complex command line options and `ruby -s` for trivial command line
  options.
* Write for Ruby 1.9. Don't use legacy Ruby 1.8 constructs.
    * Use the new JavaScript-style literal hash syntax.

        ```Ruby
        # Bad
        has_many :contact_details, :order => 'priority DESC', :dependent => :destroy

        # Good
        has_many :contact_details, order: 'priority DESC', dependent: :destroy
        ```

    * Use the new lambda syntax.

        ```Ruby
        def some_method(a, &b)
          a.call 'first block'
          b.call 'second block'
        end

        # old style
        some_method lambda { |msg| puts msg } do |msg|
          puts msg
        end

        # new style
        some_method ->(msg='default') { puts msg } do |msg|
          puts msg
        end
        ```

    * Methods like `inject` now accept method names as arguments.

        ```Ruby
        [1, 2, 3].inject(:+)
        ```

* Avoid needless metaprogramming.

<a name="design"/>
## Design

* Code in a functional way, avoiding mutation when that makes sense.
* Do not mutate arguments unless that is the purpose of the method.
* No overriding core classes when writing libraries. (No monkey patching!) Adding behaviour/classes
  can be acceptable.
* Do not [program defensively.][programming_rules] The exception is when accepting input to the
  system from outside. Within the system, all is assumed to be correct.

    ```Ruby
    # bad
    def foo(arg)
      raise ArgumentError unless arg.is_a?(String)
      # ...
    end
    ```

    ```Ruby
    def foo(arg)
      # ...
    end
    ```

* Keep the code simple and subjective. Each method should have a single, well-defined
  responsibility.
* Avoid more than three levels of block nesting.
* Don't overdesign. Overly complex solutions tend to be brittle and hard to maintain.
* Don't underdesign. A solution to a problem should be as simple as possible, but no simpler than
  that. Poor initial design can lead to a lot of problems in the future.
* Be consistent. In an ideal world, be consistent with these guidelines.
* Use common sense.

[programming_rules]: http://www.erlang.se/doc/programming_rules.shtml#HDR11

<a name="contributing"/>
# Notes

Nothing written in this guide is set in stone. I intend this document to be a suggestion. I expect
the rest of the team have their own opinions about what is ideal code style.

There are a few elements from the original that I left, here, even though they don't apply to our
needs.

Feel free to change stuff to your liking!


<a name="spreadtheword"/>
# Spread the Word

This style guide [was written by Bozhidar Batsov][upstream].

[upstream]: https://github.com/bbatsov/ruby-style-guide
