*******************
Fortran style guide
*******************

The main leading principle when contributing code to Fortran projects should be
readability. If your code can not be easily read and understood by others, it
will be hard to maintain and extend it. It should also fit well to existing
parts of the code (in style as well as in its programming paradigms) maintaining
the principle of least surprise.

Below, you find some explicit coding rules we try to follow. The list can not
cover all aspects, so also look at the existing source code and try to follow
the conventions in it.


Line length and indentation
===========================

* Maximal **line length** is **80** characters. For lines longer than that, use
  continuation lines.

* **Nested blocks** are indented by **+2** whitespaces::
    
     write(*, *) "Nested block follows"
     do ii = 1, 100
       write(*, *) "This is the nested block"
       if (ii == 50) then
         write(*, *) "Next nested block"
       end if
     end do

* **Continuation lines** are indented by **+4** whitespaces. Make sure to
  place continuation characters (`&`) both at the end of the line as well as at
  the beginning of the continuation line::

      call someRoutineWithManyParameters(param1, param2, param3, param4,&
          & param5)

  Please, try to break lines at natural places (e.g. at whitespace characters)
  and include one whitespace character after the opening ampersand in the
  continuation line.


Naming
======

The naming conventions basically follow those in the `Google Style Guide for
Java naming convention
<https://google.github.io/styleguide/javaguide.html#s5-naming>`_, with minor
modifications.

* **Variable** names follow the **lowerCamelCase** convention::

      logical :: hasComponent

* **Constants** (parameters) use **UPPER_CASE_WITH_UNDERSCORE**::
    
      integer, parameter :: MAX_ARRAY_SIZE = 100

  with the exception of constants used to define the kind parameter for
  intrinsic types, as those should be all lowercase (and short)::

      integer, parameter :: dp = kind(1.0d0)
      real(dp) :: val


* **Subroutine** and **function** names follow also the **lowerCamelCase**
  notation::

      subroutine testSomeFunctionality()
      myValue = getSomeValue(...)


* **Type** (object) names are written **UpperCamelCase**::

      type :: RealList
      type(RealList) :: myList
      

* **Module** names follow **lower_case_with_underscore** convection::

      use dftb_common_accuracy

  Underscores are used for namespacing only, so the module above would be
  typically found under the path `dftb/common/accuracy.f90`. The individual
  component names (``dftb``, ``common``, ``accuracy``) should not contain any
  underscores.


Whitespaces
===========

Please use whitespaces to make the code readable. In general, you **must use**
whitespaces in following situations:

* Around arithmetic operators::
    
      2 + 2

* Around assignment and pointer assignment operators::

      aa = 3 + 2
      pWindow => array(1:3)

* Arround the ``::`` separator in declarations::

      integer :: ind

* After comma (``,``) in generally, especially in declarations, calls, lists::

      real(wp), allocatable :: array(:)
      type, extends(BaseType) :: DerivedType
      subroutine myRoutine(par1, par2)
      call myRoutine(val1, val2)
      print *, 'My value:', val
      do ii = 1, 3
      array(1:3) = [1, 2, 3]

* When separating array indices, when the actual index value for an index
  contains an expression::

      myArray(ii + 2, jj) = 12

You **may omit** whitespace in following cases:

* When separating array indices, and the actual index values are simple and
  short (typically two letters) variable names, one or two digit integers or the
  range operator ``:``::

      myArray(:,1) = vector
      latVecs(1,1) = 1.0_wp
      myArray(ii,jj) = myArray(jj,ii)

You **must omit** whitespaces in following cases:

* Around opening and closing braces of any kind::

      call mySubroutine(aa, bb)  ! and NOT call mySubroutine( aa, bb )
      myVector(:) = [1, 2, 3]    ! instead of myVector(:) = [ 1, 2, 3 ]
      tmp = 2 * (aa + bb)        ! instead of 2 * ( aa + bb )

* Around the equal (``=``) sign, when passing named arguments to a function or
  subroutine::

      call mySubroutine(aa, optionalArgument=.true.)

**Avoid** to use whitespace for **visual aligning** of code lines. So use::

      integer, intent(in) :: nNeighbors
      real(wp), intent(out) :: interaction

instead of::

      integer, intent(in)   :: nNeighbors
      real(wp), intent(out) :: energy

Although latter may look more readable, it makes rather difficult to track real
changes in the code with the revision control system. When a new line is added
to the block making the realignment of previous (otherwise unchanged) lines
::

      integer, intent(in)             :: nNeighbors
      real(wp), intent(out)           :: energy
      real(wp), intent(out), optional :: forces(:)

the version control system will indicated them as changed lines, although only
the alignment (but not the actual instructions) where changed.


Comments
========

* Comments are indented to the same position as the code they document::

      ! Take spin degeneracy into account
      energy = 2.0_wp * energy

* Generally, write the comment *before* the code snippet it documents::
   
      ! Loop over all neighbors
      do iNeigh = 1, nNeighbors
        :
      end do

* Try to avoid mixing code and comments within one line as this is often hard to
  read::

      bb = 2 * aa   ! this comment should be before the line.

* Never use multiline suffix comments as an indenting editor would mess up
  the indentation of subsequent lines::
    
      bb = 2 * aa  ! This comment goes over multiple lines, therefore, it
                   ! should stay ALWAYS before the code snippet and NOT HERE.

* Comment eventual workarounds with special comments, which include the compiler
  name and its version number for which the workaround had to be made. Use
  always the following pattern, so that searching for workarounds which can be
  possibly removed is easy::

      ! Workaround: gfortran 4.8
      ! Finalization not working, we have to deallocate explicitely
      deallocate(myPointer)
      
      
* Comments should always start with one bang only. Comments with two bangs are
  reserved for source code documentation systems::

      ! This block needs a documentation
      do ii = 1, 2
        : 
      end do

* If you need a comment for a longer block of code, consider to package that
  block of code into a properly named function instead. (If the additional
  function call would be performance critical, write an internal procedure for
  it.)::

      somePreviousStatement
      ind = getFirstNonZero(array)
      someStatementAfter

  instead of ::

      somePreviousStatement

      ! Look for the first nonzero element
      found = .false.
      do ind = 1, size(array)
        if (array(ind) > 0) then
	  found = .true.
	  exit
	end if
      end do
      if (.not. found) then
        ind = 0
      end if
      
      someStatementAfter
