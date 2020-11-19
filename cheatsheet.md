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

### Convert timezones
More https://www.askpython.com/python-modules/python-pendulum-module

    import pendulum
    # Time in NY 2020-10-27 11 AM
    ny = pendulum.datetime(2020,10,27,11, tz='America/New_York')
    # Convert to german time
    ny.in_timezone('Europe/Berlin')  # -> DateTime(2020, 10, 27, 17, 0, 0, tzinfo=Timezone('Europe/Berlin'))

### Open file with encoding

    file -I 1234.message.in,maildir,format,S=0815,W=1272:2,S
    1234.message.in,maildir,format,S=0815,W=1272:2,S: message/rfc822; charset=iso-8859-1
    
    foo = r'1234.message.in,maildir,format,S=0815,W=1272:2,S'
    with open(foo, 'r', encoding='iso-8859-1') as f: 
        print(f.readlines())


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
    docker rm -f <name>  # stop a running container and delete it
    docker images  # list all images
    docker rmi <image-id>  # remove image

### Usefull docker commands

    docker exec -it jupyter bash  # connect to a docker container with bash
    docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' jupyter  # get the ip adress from your container

## Jupyter 
Install library in jupyter

    import sys
    !{sys.executable} -m pip install numpy 
    # To install from github
    !{sys.executable} -m pip install git+https://github.com/jerik/py-money.git#egg=py-money
    # see: https://stackoverflow.com/a/15268990/1933185

### Usefull column commands

    df.name.unique()  # List uniq values in column
