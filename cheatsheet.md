# Cheatsheet

## python
### Load file into repl to work with it
E.g. load file tinker.py

    python -i tinker.py
    # or inside repl (for python3++)
    exec(open('tinker.py').read())

### Logging and debugging
Make an logging file that has the same name as the script

    import logging, sys, pdb
    logfile = sys.argv[0].replace('.py', '.log')
    logging.basicConfig(filename=logfile, level=logging.DEBUG)
    # logging.debug('Step: %s' % guess)
    # pdb.set_trace()


Create an function that print and logs

    def hey(ho):
        print(ho)
        logging.debug(ho)


### List comprehension

    # Syntax of list comprehension
    # [expression FOR item IN list]
    [letter for letter in 'human']

    # Alternate way with lambda
    list(map(lambda x: x, 'human'))

    # one if
    [x for x in range(20) if x % 2 == 0]

    # two if's
    [x for x in range(100) if x % 2 == 0 if x % 5 == 0]

    # if ... else
    ["Even" if i % 2 == 0 else "Odd" for i in range(10)]


### Lambda

    foo = lambda x, y: x * x + y
    foo(5,2)  # => 5*5+2 = 27
    z = 10
    bar = lambda x, y: foo(x, z) + foo(y, z)
    bar(5,2)  # => 49 = 5*5+10 + 2*2+10 
    z = 5 
    bar(5,2)  # => 39 = 5*5+5 + 2*2+5 
