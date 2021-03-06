About
+++++

Snowscript is a language that compiles to PHP. Its syntax is inspired by 
Python, Lua, Ruby, Coffescript, Go and Scala and strives to be DRY, clean and 
easy to read as well as write.

Roadmap
+++++++

The current status as of May 13th, 2012 is that the basics work. There are
several things I want to fix before a first release candidate though:

Todo 0.4
========

- Webpage.
- Documentation.
- Advanced class syntax.
- Command line compile tool.
- Full examples.
- Some bugs in strings and comments.
- Tolerable error messages.
- Code cleanup.

Done
==== 

- Ternary operator.
- Control structures.
- For loops.
- Function style casts.
- Basic classes.
- Destructuring.
- Parsing of basic syntax.
- Transformations for the non LALR(1) compatible features of Snowscript like
  implicit parenthesis, the switch syntax and significant whitespace.
- Lexer.

Todo 0.5
========

- Named parameters.
- List comprehension.
- Splats.
- Loops and control structures as expressions.
- Inner functions.
- Parser written in Snowscript.
- Existance.

Todo 0.6
========

- Closures and lambdas.
- Ommision of "," inside brackets.

Todo 0.7
========

- Great error messages.
- Namespaces.

Todo 0.8
========

- Macros.

Documentation
+++++++++++++

Whitespace
==========

Snowscript has significant whitespace, meaning that the code structure is 
managed by indenting/dedenting and not by using brackets "{}". Whitespace is 
not significant inside strings and brackets "()[]", except in closures.

snowscript::

    fn is_it_big(number)
        if number < 1000
            <- YES
        else
            <- NO

php::

    function is_it_big($number) {
        if ($number < 1000) {
            return YES;
        } else {
            return NO;
        } 
    }

Inside brackets "()[]", a new line can be used as a separator instead of ",". 
One line structures is done with a ":".

snowscript::
    
    ordering = [
        first()
        second()
        third()
    ]
    fn double(x): <- x * 2
    
php::

    $ordering = array(
        first(),
        second(),
        third(),
    );
    function double($x) {
        return $x * 2;
    }

Comments
========

snowscript::

    # Single line.
    # Single line as docblock. #
    # This is a docblock,

      spanning multiple lines.

php::

    // Single line.
    /**
     * Single line as docblock. 
     */
    /**
     * This is a docblock,
     *
     * spanning multiple lines. 
     */

Arrays and dictionaries
=======================

Array and dictionaries are defined using square brackets "[]". Items are 
separated by "," or a new line. 

Dictionaries are defined with key/value pairs seperated with "=" or ":". Keys 
are stringy when using "=" as a separator, and interpreted when using ":".

snowscript::

    pianists = ["McCoy Tyner", "Fred Hersch", "Bill Evans"]
    
    name = "Heroes"
    series = [
        name:
            genre = "Science Fiction"
            creator = "Tim Kring"
            seasons = 4
        "Game Of Thrones":
            genre = "Medieval fantasy"
            creator = "David Benioff"
            seasons = 2
    ]
    
php::

    $pianists = array("McCoy Tyner", "Fred Hersch", "Bill Evans");
    
    $name = "Heroes";
    $series = array(
        $name => array(
            'genre' => "Science Fiction",
            'creator' => "Tim Kring",
            'seasons' => 4,
        ),
        "Game Of Thrones" => array(
            'genre' => "Medieval fantasy",
            'creator' => "David Benioff",
            'seasons' => 2,
        ),
    );
    
Accessing items is done using square brackets "[]" or by using the "|" shortcut. 
Integers and the regex "[A-Za-z_][A-Za-z0-9_]+" can be used with the "|" 
shortcut.

snowscript::

    echo answers|0|options|0|help_text
    echo answers[0]['options'][0]['help_text']

php::

    echo $answers[0]['options'][0]['help_text'];
    echo $answers[0]['options'][0]['help_text'];

Arrays can be defined without using "[]" when not in a bracket "[]()" context.

snowscript::

    fn phone_home
        <- dial(NUMBER), 0
    message, status = phone_home()

php::

    function phone_home() {
        return array(dial(NUMBER), 0);
    }
    list($message, $status) = phone_home();

Anonymous functions inside bracket context can use arrays without "[]" too.

snowscript::

    call_a_friend(
        fn
            <- "Sweden", 123456789
    )

php::

    call_a_friend(
        function {
            return array("Sweden", 123456789);
        }
    );

Strings
=======

There are four kind of strings: '"""', '"', "'''" and "'", all multiline.

Whitespace before the current indentation level is stripped. A single empty line 
in the beginning or end is stripped too if present. Strings can be concatenated 
using the "%" operator.

snowscript::

    echo "I am" % " legend!"

php::

    echo "I am" . " legend!";

Quoted
------

Code inside "{}" adds their value to the string.

snowscript::

    fn travel
        echo "
        The {animal} went to {world.place()}
        with his {NUM} friends. 
        "

    """<a href="https://snowscript.org">Snowscript</a>\n"""

php::

    function travel() {
        echo "The " . $animal . " went to " . $world->place() . "\n"
        " with his " . NUM  . " friends.";
        
    }
    "<a href=\"https://snowscript.org\">Snowscript</a>\n";

Unquoted
--------

snowscript::

    'No {magic} here\n'
    '''{nor()} here.'''

php::

    'No {magic} here\n';
    '''{nor()} here.''';

Functions
=========

The "fn" keyword is used to define functions, and "<-" to return a value.

Function calls can be chained using the "->" operator that passes the expression
before as the first argument to the next function.

snowscript::

    fn titlefy(FancyString fancystring)
        <- fancystring->trim(" -")->ucfirst()

php::

    function titlefy(FancyString $fancystring) {
        return ucfirst(trim($fancystring, " -"));
    }
    
Arguments passed as reference must have a prefixing "&".

snowscript::

    fn init_ab(&a, &b)
        a = 10
        b = 10
    init_ab(&a, &b)
    
php::

    function init_ab(&$a, &$b) {
        $a = 10;
        $b = 10;
    }
    init_ab($a, $b);

Optional parameters
-------------------

Optional parameters must come after required parameters. They can be passed 
"null" to select the default value. This is helpful if you want to set a later
parameter to a non-default value.

snowscript::

    fn make_pretty(text, font="Rocky", size=84)
        pass
    make_pretty("Snowscript", null, 42)
    
php::
    
    function make_pretty($text, $font=null, $size=null) {
        if ($font === null) {
            $font = "Rocky";
        }
        if ($size === null) {
            $size = 84;
        }
    }
    make_pretty("Snowscript", null, 42);

Named parameters
----------------

Named parameters is supported using an array "[]" at the end of the function 
declaration. Named parameters with only a key are required, i.e. an exception
will be thrown if absent.

Optional and named parameters can not be mixed in the same function definition.

snowscript::

    fn render(template, [mood, color, allow_html=true, klingon=false])
        echo mood()
    render("index.html", klingon=true, mood="faul", color="red")

php::

    function render($template, $options_) {
        $defaults_ = array(
            'format' => "html", 
            'allow_html' => true, 
            'klingon' => false,
        );
        $options_ += $defaults_;
        $required_ = array('mood', 'color');
        foreach ($required_ as $key_) {
            if (!isset($options_[$key_])) {
                throw new InvalidArgumentException("'$key_' is a required option.");
            }
        }
        unset($key_);
        echo $options_['mood'];
    }
    render("index.html", array('klingon'=>true, 'mood'=>"faul", 'color'=>"red"));

Inner functions
---------------

Functions inside functions, is defined at compile time, and are only available
inside the scope where they are defined. Nesting can go arbitrarily deep.

snowscript::

    fn wash_car(Car car)
        fn apply_water(car)
            pass
        fn dry(car)
            pass
        <- car->apply_water()->dry()

php::
    
    function _wash_car_apply_water_($car) {}
    function _wash_car_dry_($car) {}
    function wash_car(Car $car) {
        return _wash_car_dry_(_wash_car_apply_water_($car));
    }

Closures
--------

Closures are multiline controlled by indentation. A "+" before the variable name
binds a variable from the outer scope.

snowscript::
    
    use_me = get_use_me()
    little_helper = fn(input, +use_me)
        <- polish(input, use_me)
    little_helper(Lamp())
    
    takes_functions(
        fn(x)
            <- [x * 2, x * x]
        fn(y, c)
            <- y * c
    )

php::

    $use_me = get_use_me();
    $little_helper = function($input) use ($use_me) {
        return polish(input, $use_me);
    }
    little_helper(new Lamp);
    
    takes_functions(
        function(x) {
            return array(x * 2, x * x);
        },
        function(y, c) {
            return array(y * c);
        }
    )
    
Lambdas
-------

Single line closures, that can only return a single expression. The "<-" return
keyword is omitted.

snowscript::

    filter(coll, fn(x): x > 3, true)
    
php::

    filter($coll, function() { return $x > 3; }, true);

Destructuring
=============

Snowscript has simple destructuring.

snowscript::

    a, b, c = 1, 2, 3
    [a, b, [c, d]] = letters

php::

    list($a, $b, $c) = [1, 2, 3];
    list($a, $b, list($c, $d)) = $letters;

Control structures
==================

Three control structures are available: "if", "switch" and the ternary operator.

if
--

snowscript::

    if white_walkers.numbers < 500
        fight_valiantly()
    elif feeling_lucky
        improvise()
    else
        run()


php::

    if ($white_walkers->numbers < 500) {
        fight_valiantly();
    } elif ($feeling_lucky) { 
        improvise();
    } else {
        run();
    }

switch
------

snowscript::

    switch gamestate
        BESERKER
            signal("searchanddestroy")
        UNDERWATER
            gills.activate()
        NORMAL, default
            signal("playnice")
            gills.deactivate()


php::

    switch $gamestate {
        case BESERKER:
            signal("searchanddestroy");
            break;
        case UNDERWATER:
            $gills->activate();
            break;
        case NORMAL:
        default:
            signal("playnice");
            $gills->deactivate();
    }

Return
------

Both if and switch statements can be used as an expression.

snowscript::

    mood = if prince.is_in_the_house
        <- "Exquisite"
    else
        <- "Dull"

php::

     if ($prince->is_in_the_house) {
        $mood = "Exquisite";
    } else {
        $mood = "Dull";
    };

Ternary operator
----------------

Ternary operator is a oneline ``if a then b else c`` syntax.

snowscript::

    echo if height > 199 then "tall" else "small"
    
php::

    echo ($height > 199 ? "tall" : "small");


Existence
=========

There are two existence shortcuts "?" and "??". The first is a shortcut for
``isset(expr)`` the second for ``!empty(expr)``.

snowscript::

    if get_result()?
        do_stuff()

    if get_result()??
        do_stuff()

php::

    $tmp_ = get_result(); 
    if (isset($tmp_)) {
        do_stuff();
    }
    unset($tmp_);

    $tmp_ = get_result(); 
    if (!empty($tmp_)) {
        do_stuff();
    }
    unset($tmp_);
        

Type casting
============

To cast an expression to a type, use the `array`, `bool`, `float`, `int`, 
`object` or `str` functions.

php::

    array(a)

php::

    (array) $a;

Loops
=====

For
---

Two kind of for loops are supported. Iterating over a collection, and iterating 
over a numeric range. Both key and value are local to the loop. An "&" can be 
used to designate the value as by-reference.

snowscript::

    for title, data in flowers
        echo "{data.id}: title"
    for &n in numbers
        n *= 2

    for i in 1 to 10 step 2
        echo i
    for i in 10 downto 1
        echo i

php::

    foreach ($flowers as $title => $data) {
        echo $data->id . ": " . $title;
    }
    unset($title, $data);
    foreach ($numbers as $n) {
        $n *= 2;
    }
    unset($n);

    for ($i=1, $i <= 10, $i+=2) {
        echo $i;
    }
    unset($i);
    for ($i=10, $i >= 0, --$i) {
        echo $i;
    }
    unset($i);

While
-----

snow::
    
    while frog.ass.is_watertight
        echo "Rinse and repeat."

php::

    while ($frog->ass->is_watertight) {
        echo "Rinse and repeat.";
    }

Array comprehension
===================

Snowscript has array comprehension similiar to that of Python and others.

snowscript::

    [x, y for x in [1,2,3] for y in [3,1,4] if x != y]->var_dump
    
    fights = [fight(samurai, villain)
              for samurai in seven_samurais
                  if samurai->is_awake()
              for villain in seven_vaillains
                  if not villain->is_in_jail()
    ]

php::

    $result_ = array();
    foreach (array(1, 2, 3) as $x) {
        foreach (array(3, 1, 4) as $y) {
            if ($x != $y) {
                $result_[$x] = $y;
            }
        }
    }
    unset($x, $y);
    var_dump($result_);

    $fights = array();
    foreach ($seven_samurais as $samurai) {
        if (!$samurai->is_awake()) {
            continue;
        }
        foreach ($seven_villains as $villain) {
            if ($villain->is_in_jail()) {
                continue;
            }
            $fights[] = fight($samurai, $villain);
        }
    }
    unset($samurai, $villain);

Naming conventions
==================

Snowscript uses naming conventions to strip out some of PHP's operators. 
Classes are PascalCase, constants are ALL_CAPS while variables, methods and
functions are whats left.

snowscript::
    
    foo    
    foo()
    Foo()
    FOO
    
    bar.foo
    bar.foo()
    bar.FOO
    Bar.foo
    Bar.FOO
     
    
php::

    $foo;
    foo();
    new Foo;
    FOO;
    
    $bar->foo;
    $bar->foo();
    $bar::FOO;
    Bar::$foo;
    Bar::FOO;

Mechanisms for working with non-compliant PHP code will be made available
as macros.
    
Snowscript uses scope information to determine when a name is a callable
variable and when it's a function call.

snowscript::

    call_me()
    
    cb1 = get_callback()
    cb1()
    
    set_callback(&cb2)
    cb2()

php::

    call_me();
    
    $cb1 = get_callback();
    $cb1();
    
    set_callback($cb2);
    $cb2();
    
Classes
=======

Declaration
-----------

The arguments to the class is given after the class name and are available to 
use to set propertes.

The "." is used to access the class instance. "self" accesses the class.

snowscript::

    class TabularWriter(File path, title)
        # Properties. #
        title = title
        _filehandle = null
        
        # Constants. #
        VERSION = 0.4
        
        # Static members.
        static filesystem = Filesystem().get()
        
        # Constructor. #
        fn __construct
            .check_filesystem()
            .init_file(path)
            
        # Methods. #
        fn check filesystem
            if not filesystems()[self.filesystem]?
                throw UnsupportedFilesystemError()

        fn init_file(path)
            if not file_exists(path)
                throw FileMissingError()
            else
                ._filehandle = open_file(path)

php::

    class TabularWriter {
        /**
         * Properties.
         */
        public $title;
        public $_filehandle;
        
        /**
         * Constants.
         */        
        const VERSION = 0.4;
        
        /**
         * Static members.
         */
        static filesystem = null;

        /**
         * Constructor.
         */
        public function __construct(File path, title) {
            $this->title = $title;
            $filesystem_ = new Filesystem;
            self::$filesystem = $filesystem_.get();
            unset($filesystem_);
            $this->check_filesystem();
            $this->init_file($path);
        }

        /**
         * Methods.
         */
         
        public function check_filesystem() {
            $tmp_ = supported_filesystems();
            if (!isset($tmp_[self::$filesystem])) {
                throw new UnsupportedFilesystemError;
            }
            unset($tmp_);
        }

        public function init_file($path) {
            if (!file_exists($path)) {
                throw new FileMissingError;
            } else {
                $this->filehandle = open_file($path);
            }
        }
    }
    TabularWriter::$filesystem = Filesystem().get()
    
Protected and private visibility is supported but not considered very "snowy", 
after all "We're all consenting adults here". Instead it's recommended to prefix
members with a "_" to mark them as subject to change.

The "final", "static" and "const" keywords are supported as well.

Functions and properties can be indented below modifier keywords.

snowscript::

    abstract class FactoryFactory extends AbstractBuilder interfaces FactoryFactoryInterface
        const DEFAULT_FACTORY = "DefaultFactory"

        protected static 
            factories = []
            version = 1.0

        public static fn getInstance(factoryClassName)
            <- self.factories[factoryClassName]

php::

    abstract class FactoryFactory extends AbstractBuilder interfaces FactoryFactoryInterface {
        const DEFAULT_FACTORY = "DefaultFactory";

        protected static $factories = [];
        protected static $version = 1.0;

        public static function getInstance($factoryClassName) {
            return self::factories[$factoryClassName];
            
        }
            
    }

Usage
-----

Class instantiation uses function notation.

snowscript::

    Bicycle(Rider())

php::

    new Bicycle(new Rider));

Properties and methods on instantiated classes is accessed with the "."
operator. Using "." after a ClassName access static members.

snowscript::

    wind = Wind(52, 12)
    wind.blow()
    Newspaper().read()
    
    Player.register("Ronaldo")
    Player.MALE
    Player.genders

php::

    $wind = Wind(52, 12);
    $wind->blow();
    (new Newspaper())->read();
    
    Player::register("Ronaldo");
    Player::MALE;
    Player::$genders;

Operators
=========

A number of operators has changed from PHP.

================= ============================
PHP               Snow
================= ============================
&&                and
!                 not
||                or
and               _and_ (Not recommended)
or                _or_ (Not recommended)
%                 mod
$a  %= $b         a mod= b
.                 %
$a .= $b          a %= b
&                 band
|                 bor
^                 bxor
<<                bleft
>>                bright
~                 bnot
================= ============================

Namespaces
==========

I'm still undecided on the use of namespaces in Snowscript.

Macros
======

Stub.
