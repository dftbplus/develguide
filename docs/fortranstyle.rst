*******************
Fortran style guide
*******************

The main principle when contributing code to Fortran projects should be
readability. If your code can not be easily read and understood by others it
will be hard to maintain and extend. It should also fit well with the existing
parts of the code (in style as well as in its programming paradigms) maintaining
the principle of least surprise.

Below you will find some explicit coding rules we try to follow. The list can
not cover all aspects, so also look at the existing source code and try to
follow the conventions being used.

If you use Emacs as editor, consider adding `appropriate customisation settings
<https://gist.github.com/aradi/68a4ff8430a735de13f13393213f0ea8>`_ to your
config file in order to automatically enforce some of the conventions below.


Line length and indentation
===========================

* Maximal **line length** is **100** characters. For lines longer than that, use
  continuation lines.

* **Nested blocks** are indented by **2** white spaces::
    
     write(*, *) "Nested block follows"
     do ii = 1, 100
       write(*, *) "This is the nested block"
       if (ii == 50) then
         write(*, *) "Next nested block"
       end if
     end do

* **Continuation lines** are indented by **4** white spaces. Make sure to
  place continuation characters (`&`) both at the end of the line as well as at
  the beginning of the continuation line::

      call someRoutineWithManyParameters(param1, param2, param3, param4,&
          & param5)

  Try to break lines at natural places (e.g. at white space characters) and
  include one white space character after the opening ampersand in the
  continuation line.

* **Single line preprocessor directives** are indented as normal code::

      @:ASSERT(someCondition)
      call someRoutine(...)

* **Preprocessor block directives** (directives with starting and ending
  constructs) are outdented by **2** characters with respect of the code they
  enclose. The enclosed code must be aligned as if the preprocessor directives
  were not present::

      call doSomething()
    #:if WITH_SCALAPACK
      call someRoutineScalapackVersion(...)
    #:else
      call someRoutineSerialVersion(...)
    #:endif

    do iKS = 1, nKS
    #:if WITH_SCALAPACK
      call someRoutineScalapackVersion(iKS, ...)
    #:else
      call someRoutineSerialVersion(iKS, ...)
    #:endif
    end do

    
    
Naming
======

The naming conventions basically follow those in the `Google Style Guide for
Java naming convention
<https://google.github.io/styleguide/javaguide.html#s5-naming>`_, with minor
modifications.

* **Variable** names follow the **lowerCamelCase** convention::

      logical :: hasComponent

* **Constants** (parameters) use the **lowerCamelCase** convention similar to
  variables ::
    
      integer, parameter :: maxArraySize = 100

  with the exception of the constants used to define the kind parameter for
  intrinsic types, which should be all lowercase (and short)::

      integer, parameter :: dp = kind(1.0d0)
      real(dp) :: val


* **Subroutine** and **function** names follow also the **lowerCamelCase**
  notation::

      subroutine testSomeFunctionality()
      myValue = getSomeValue(...)


* **Type** (object) names are written **UpperCamelCase**::

      type :: TRealList
      type(TRealList) :: myList

  All type names should be prefixed with a capital 'T', in order to clarify the
  distinction between type names and variable names::

      type :: TBroydenMixer
      :
      end type TBroydenMixer
      :
      type(TBroydenMixer) :: broydenMixer
      

* **Module** names follow **lower_case_with_underscore** convention::

      use dftb_common_accuracy

  Underscores are used for name-spacing only, so the module above would be
  typically found at the path `dftb/common/accuracy.f90`. The individual
  component names (``dftb``, ``common``, ``accuracy``) may not contain any
  underscores and must be shorter than 15 characters.


* **Preprocessor** variables and macros follow **UPPER_CASE_WITH_UNDERSCORE**
  convention::

    #:if WITH_MPI
      withMpi = ${FORTRAN_LOGICAL(WITH_MPI)}$
    #:endif


White spaces
============

Please use white spaces to make the code readable. In general, you **must use**
white spaces in following situations:

* Around arithmetic operators::
    
      2 + 2

* Around assignment and pointer assignment operators::

      aa = 3 + 2
      pWindow => array(1:3)

* Around the ``::`` separator in declarations::

      integer :: ind

* After commas (``,``) in general and especially in declarations, calls and
  lists::

      real(wp), allocatable :: array(:)
      type, extends(TBaseType) :: TDerivedType
      subroutine myRoutine(par1, par2)
      call myRoutine(val1, val2)
      print *, 'My value:', val
      do ii = 1, 3
      array(1:3) = [1, 2, 3]

* When separating array indices, when the actual index value for an index
  contains an expression::

      myArray(ii + 2, jj) = 12

You **may omit** white space in following cases:

* When separating array indices and the actual index values are simple and
  short (typically two letters) variable names, one or two digit integers or the
  range operator ``:``::

      myArray(:,1) = vector
      latVecs(1,1) = 1.0_wp
      myArray(ii,jj) = myArray(jj,ii)

You **must omit** white spaces in following cases:

* Around opening and closing braces of any kind::

      call mySubroutine(aa, bb)  ! and NOT call mySubroutine( aa, bb )
      myVector(:) = [1, 2, 3]    ! instead of myVector(:) = [ 1, 2, 3 ]
      tmp = 2 * (aa + bb)        ! instead of 2 * ( aa + bb )

* Around the equal (``=``) sign, when passing named arguments to a function or
  subroutine::

      call mySubroutine(aa, optionalArgument=.true.)

* Around the power operator::

      val = base**power   (instead of val = base ** power)

**Avoid** white spaces for **visual aligning** of code, use::

      integer, intent(in) :: nNeighbors
      real(wp), intent(out) :: interaction

instead of::

      integer, intent(in)   :: nNeighbors
      real(wp), intent(out) :: energy

Although latter may look more readable, it makes rather difficult to track real
changes in the code with the revision control system. For example when a new
line is added to the block making the realignment of previous (but otherwise
unchanged) lines necessary ::

      integer, intent(in)             :: nNeighbors
      real(wp), intent(out)           :: energy
      real(wp), intent(out), optional :: forces(:)

the version control system will indicate all of those lines having been
modified, although only the alignment (but not the actual instructions) were
changed.


Comments
========

* **Module**, **Subroutine** and **function** comments should be consistent with
  `doxygen <http://doxygen.org/>`_ / `FORD
  <https://github.com/cmacmackin/ford>`_ literate comments for publicly visible
  interfaces and variables.

* Comments are indented to the same position as the code they document::

      ! Take spin degeneracy into account
      energy = 2.0_wp * energy

* Generally, write the comment *before* the code snippet it documents::
   
      ! Loop over all neighbours
      do iNeigh = 1, nNeighbours
        :
      end do

* Try to avoid mixing code and comments within one line as this is often hard to
  read::

      bb = 2 * aa   ! this comment should be before the line.

* Never use multi-line suffix comments, as an indenting editor would mess up the
  indentation of subsequent lines::
    
      bb = 2 * aa  ! This comment goes over multiple lines, therefore, it
                   ! should stay ALWAYS before the code snippet and NOT HERE.

* Specifically comment any workarounds, include the compiler name and the
  version number for which the workaround had to be made. Always use the
  following pattern, so that searching for workarounds which can be possibly
  removed is easy::

      ! Workaround: gfortran 4.8
      ! Finalisation not working, we have to deallocate explicitly
      deallocate(myPointer)
      
      
* Comments should always start with one bang only. Comments with two bangs are
  reserved for source code documentation systems::

      ! This block needs a documentation
      do ii = 1, 2
        : 
      end do

* If you need a comment for a longer block of code, consider instead packaging
  that block of code into a properly named function (if the additional function
  call would be performance critical, write it as an internal procedure)::

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

Allocation status
=================

At several places, the allocation status of a variable is used to signal choices
about logical flow in the code::
  
  !> SCC module internal variables
  type(TScc), allocatable :: sccCalc
  .
  .
  .
  if (allocated(sccCalc)) then

  end if

This is to be preferred to the use of additional logical variables if possible.

Part of the reason for this choice is that from Fortran 2008 onwards, optional
arguments to subroutines and functions are treated as not-present if not
allocated.
