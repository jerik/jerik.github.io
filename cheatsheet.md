# Cheatsheet

## python
### Formatting

	print("$ {:,.2f}".format(155.23434))

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

## Pandas 
### Display options 

    pd.set_option('display.max_row', 1000)  # show max 1000 rows, avoid summarizing
	pd.set_option('display.max_rows', None) # Unlimited rows
	pd.set_option('display.max_columns', None) # Unlimited columns

### Creating dataframe from list of dicts

	rows = [
		{ 'konto': 'foo', 'plus': 10, 'minus', -20 }
		{ 'konto': 'bar', 'plus': 30, 'minus', -40 }
	]
	df = pd.DataFrame(rows, columns=['konto', 'plus', 'minus'])

### Read CSV 

	import pandas as pd 
	df = pd.read_csv('../file.csv', sep=';')

### Get column headers

	df.columns
	df.columns.values
	sorted(df)

### change column headers

	df.rename(columns={'Foo':'Foobar'}, inplace=True) # Change one column header
	df.columns = [x.lower() for x in sorted(df)]  # set all column headers, here in small cases

### selecting commands
see https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.loc.html
Oops: Both loc and iloc are row-first, column-second. This is the opposite of what we do in native Python, which is column-first, row-second.

    df.loc[2, 'column_name']  # show single value at row 2 and in column_name
      df.column_name.loc[2]  
      df.column_name.iloc[2]  
      df.iloc[2, 1]  # assuming column_name is on position 1
      df.column_name[2] 
    df.loc[1:3, column_name]  # show values at rows 1 - 3 in column_name 
    df.loc[:3, column_name]  # show values at rows start - 3 in column_name 
      df.column_name.loc[:3]  
      df.column_name.iloc[:2]  
      df.iloc[:2, 1] # assuming column_name is on position 1
      df.column_name.head(3)  
    df.loc[[1,4,8], 'column_name']  # select multiple values in column_name
      df.column_name.loc[1,4,8] 
      df.column_name.iloc[1,4,8] 
      df.iloc[[1,4,8], 1 ]  # assuming column_name is on position 1
    df.loc[[1,4,8], ['column_1', 'column_2']]  # select multiple values in serveral columns
    df.loc[3:, column_name]  # show values at rows 3 - end in column_name 
    df.loc[:, column_name]  # show whole column_name values
    df.loc[df.column_name == 'pattern']  # selecting rows with 'pattern' in column_name
    df.loc[(df.column_name.isin(['pat1', 'pat2'])) & (df.other_column > 100)]  # selecting with 2 conditions
    df.loc[df.column_name.idxmax(), 'title']  # select the title cell with the highest values in the column_name
    df.column_name.str.contains('foo').sum()  # show amount of matches for the string 'foo' in the column
	df.loc[df.col == 'pattern', 'col2']  # show on filtered rows only col2

### changing commands

	df['new'] df['col'].str.replace(',', '.')  # replaces all , with . (german to english number format)
	df['new'] = df['col'].str.replace(',','.').astype(float)  # replaces and convert it to float


### setting commands

    df.loc[2, 'column_name'] = 12  # set value at row 2 in column_name to 12
	df['new_col'] = df['old_col']  # create a new column with the data from the old column

### deleting commands

	df.drop('col', axis=1, inplace=True)  # axis: 0 => rows, 1 => cols
### Usefull column commands

    df.column_name.unique()  # List uniq values in column
    df.column_name.value_counts()  # List uniq values with amount of occurness
    df.column_name.describe()  # short summary, depends on source numbers vs. text
    df.column_name.mean()  # show mean
    df['column_name'].isnull()  # Check if (known) null types (NA, NaN, " ") are available
    df['column_name'].fillna(12, inplace=True)  # replace missing values to 12. Important inplace!
    df['column_name'] = df['column_name'].replace(['old'],'new')  # replace a single value
    df['column_name'] = df['column_name'].replace(['old1', 'old2'],'new')  # replace a multiple values with single value
    df['column_name'] = df['column_name'].replace(['old1', 'old2'],['new1', 'new2'])  # replace a multiple values with single value

### Data Grouping and sorting

    df.groupby('column_name').size()  # get a list of values and their amount
    df.groupby('column_name')['second_column'].agg([min,max])  # show min, max values of the second_column, based on column_name
    df.groupby(['column_name', 'second_column']).size().sorte_values()  # Combination of column_name and second_column, sorted by counts

### Usefull entire dataframe commands

    df = df.replace(['old'],'new')  # replace value in entire dataframe 

### Data cleaning

    # remember to do this after you have done basic cleaning: changed unexpexted types and non standard missing values
    df.isnull().sum()  # overview of all missing values in the dataframe
    df.isnull().values.any()  # are there any missing values
    df.isnull().sum().sum()  # total number of missing values
	df.info()  # if all columns have same number of non-null items, perfect. Otherwise you have to clean up

Recognice non standard missing values by pandas

    # specifiy non standard missing values
    missing_values = ['n/a', 'na', '--']  # standard is: NA, NaN, ' '
    df = read_csv('example.csv', na_values=missing_values)
    df.name.is_null()  # will now show all missing values recognized by pandas

Change unexpected type to know missing value, see: https://www.youtube.com/watch?v=ZOX18HfLHGQ
Find unexpected types and change it to known missing value via np.nan (numpy). e.g. with try: expext

### Mapping data / maps

    my_mean = df.column_name.mean(); df.column_name.map(lambda cell: cell - my_mean)  # Do an operation on each cell value of the column
    my_mean = df.column_name.mean(); df.column_name - my_mean  # use pandas builtin, to achive the same result. Pandas guess what to do
    def do2(row): return row*2; df.apply(do2, axis='column_name')  # Apply function on each cell on the 'whole' dataframe
    df.col_1 + " -  " df.col_2  # Merge data of the two cell in each column; Oops columns must have the same length

### Pivote tables

	import numpy as np
	pd.pivot_table(df, index='konto', values='wert', aggfunc=np.sum)  # konto = col where the grouping shall take place; wert = values



### Usefull Resources
- https://datatofish.com/pandas-series-from-list/

