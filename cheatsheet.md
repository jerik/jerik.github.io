# Cheatsheet

## python
### Load file into repl to work with it
E.g. load file tinker.py

    python -i tinker.py
    # or inside repl (for python3++)
    exec(open('tinker.py').read())

### Using main()
Use a main() methode which is called automatically if it is run as script from the console.

    def main(): 
      print('do something')

    if __name__ == '__main__':
        main()

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

### Ternary Operators
For better explanation see: https://book.pythontips.com/en/latest/ternary_operators.html

    # Syntax: value_fi_true if condition else value_if_false
    state = 'nice' if True else 'ugly'  #  => nice

    # Shorthand Syntax: True or "Some", good to define function parameters
    variable = 'foobar'
    msg = variable or "No data in variable"  #  => foobar


## Docker
Usefull cheat sheet: https://dockerlabs.collabnix.com/docker/cheatsheet/

### Start an always on docker container
Named jupyter. If jupyter/scipy-notebook not locally available it will be downloaded and used
Open Notebook under https://localhost:8888 in your browser

    docker run --name jupyter --restart unless-stopped -dp 8888:8888 jupyter/scipy-notebook

### Get the token of the jupyter instance in the container

    docker exec jupyter bash -c "jupyter notebook list"

### Management of docker images / containers

    docker ps -a  # list all containers
    docker stop <name>  # stop container
    docker rm <name>  # delete container
    docker -f rm <name>  # stop a running container
    docker images  # list all images
    docker rmi <image-id>  # remove image


