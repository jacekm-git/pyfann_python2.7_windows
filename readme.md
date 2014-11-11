#Precompiled Fann 2.1 bindings for python 2.7 under Windows

##How to compile:
### 1. Swig installation:
  * download http://sourceforge.net/projects/swig/files/swigwin/
  * extract files to C:\swigwin\
  * edit system path variable
    * rigth click MyComputer -> properties
    * "advanced" tab -> "Environment Variables"
    * edit "Path" variable -> add ";C:\swigwin\;C:\python27\" on the end of line.

### 2. Microsoft Visual C++ Studio 2008 Express:
  * download and install : http://go.microsoft.com/?linkid=7729279 

### 3. Fann 2.1
  * download: http://sourceforge.net/projects/fann/files/fann/2.1.0beta/fann-2.1.0beta.zip/download  
  * extract files to : C:\fann\

### 4. Compile fanndoubled.dll
  * open C:\fann\MicrosoftWindowsDll\windll.vcproj in Visual C++ Studio 2008
  * Build -> Build windll
  * copy files fanndoubled.dll, fanndoubled.lib to C:\fann\python\pyfann

### 5. Swig
  * open command prompt

``` sh
cd c:\fann\python\pyfann\
swig -c++ -python pyfann.i
```

  * open in Visual C++ Studio 2008 C:\fann\python\pyfann.wrap.cxx
    *  find line( ctrl+f) : SWIGINTERN PyObject     *_wrap_training_data_parent_create_train_from_callback

```c++
SWIGINTERN PyObject *_wrap_training_data_parent_create_train_from_callback(PyObject *SWIGUNUSEDPARM(self), PyObject *args) {
  PyObject *resultobj = 0;
  FANN::training_data *arg1 = (FANN::training_data *) 0 ;
  unsigned int arg2 ;
  unsigned int arg3 ;
  unsigned int arg4 ;
  void (*arg5)(unsigned int,unsigned int,unsigned int,fann_type *,fann_type *) = (void (*)(unsigned int,unsigned int,unsigned int,fann_type *,fann_type *)) 0 ;
  // .... rest of file ...
```

change to:

```c++
typedef void (__stdcall *arg5_fn)(unsigned int, unsigned int, unsigned int, fann_type*, fann_type*); // added line 
SWIGINTERN PyObject *_wrap_training_data_parent_create_train_from_callback(PyObject *SWIGUNUSEDPARM(self), PyObject *args) {
  PyObject *resultobj = 0;
  FANN::training_data *arg1 = (FANN::training_data *) 0 ;
  unsigned int arg2 ;
  unsigned int arg3 ;
  unsigned int arg4 ;
  /* void (*arg5)(unsigned int,unsigned int,unsigned int,fann_type *,fann_type *) = (void (*)(unsigned int,unsigned int,unsigned int,fann_type *,fann_type *)) 0 ;*/
  arg5_fn arg5= (arg5_fn)NULL; // added line
  // ....
```

### 6. setup.py
  * open in editor C:\fann\python\pyfann\setup.py
  * find and comment lines:

```python
# swig_cmd = 'swig -c++ -python pyfann/pyfann.i'
# print 'Running SWIG before:', swig_cmd
# os.system(swig_cmd)
```

  * change setup lines to:

``` python
ext_modules=[Extension('pyfann._libfann',['pyfann/pyfann_wrap.cxx'], 
                            include_dirs=['../src/include'], 
                            # extra_objects=['../src/doublefann.o'], 
                            libraries = ['pyfann/fanndoubled'],
                            define_macros=[("SWIG_COMPILE",None)]
                            ),
```

### 7. installation
```sh
cd C:\fann\python\
python setup.py install
copy C:\fann\python\pyfann\fanndoubled.dll C:\Python27\Lib\site-packages\pyfann\
```
