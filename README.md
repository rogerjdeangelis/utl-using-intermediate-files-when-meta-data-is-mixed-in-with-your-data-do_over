# utl-using-intermediate-files-when-meta-data-is-mixed-in-with-your-data-do_over
Using intermediate files when meta data is mixed in with your data do_over
    Using intermediate files when meta data is mixed in with your data do_over

    Sometimes data manipulation is easier with files rather than SAS tables

    This can also be simply done withoy %doove or %arrau but I don't think it is as clear.

    github
    http://tinyurl.com/yy9zja26
    https://github.com/rogerjdeangelis/utl-using-intermediate-files-when-meta-data-is-mixed-in-with-your-data-do_over

    SAS Forum
    http://tinyurl.com/y6bchk3s
    https://communities.sas.com/t5/SAS-Programming/Creating-a-data-set-of-a-smaller-group-of-fields-based-on/td-p/528836

    INPUT
    =====

    filename ft15f001 "d:/txt/have.txt";
    parmcards4;
     1 2 3 4 5 6 7 8 9
     A A A B B B C C C
     1 0 1 0 1 0 1 0 1
     0 1 1 1 0 0 1 1 0
     1 1 1 1 0 0 0 0 1
    ;;;;
    run;quit;

     d:/txt/have.txt

       1 2 3 4 5 6 7 8 9
       A A A B B B C C C
       1 0 1 0 1 0 1 0 1
       0 1 1 1 0 0 1 1 0
       1 1 1 1 0 0 0 0 1


    RULES Split have.text vertically into three tables, A, B and C

    EXAMPLE OUTPUT
    --------------

    WORK.A total obs=5

      A1    A2    A3

      1     2     3
      A     A     A
      1     0     1
      0     1     1
      1     1     1

    WORK.B total obs=5

      B4    B5    B6

      4     5     6
      B     B     B
      0     1     0
      1     0     0
      1     0     0

    WORK.C total obs=5

      C7    C8    C9

      7     8     9
      C     C     C
      1     0     1
      1     1     0
      0     0     1


    SOLUTION
    ========

    %symdel cans grps;
    %deleteMacArray(outs);

    data _null_;
      length cans grps $200;
      infile "d:/txt/have.txt";
      input #1 (n1-n9) ($2.) #2 (a1-a9) ($2.);  * key statement;
      array ns $2 n1-n9;
      array as $2 a1-a9;
      do over ns;
        cans=catx(" ",cans,cats(as,ns));
        if index(grps,as)=0 then grps=catx(" ",grps,cats(as));
        put as=  grps=;
      end;
      call symputx("cans",cans);
      call symputx("grps",grps);
      stop;
    run;quit;

    %array(outs,values=&grps);

    /*
    * key macro variables created;

    GLOBAL OUTS1 A
    GLOBAL OUTS2 B
    GLOBAL OUTS3 C
    GLOBAL OUTSN 3

    GLOBAL CANS A1 A2 A3 B4 B5 B6 C7 C8 C9
    */

    data
      %do_over(outs,phrase=%str(?(keep=?:)));

      infile "d:/txt/have.txt";
      input ( &cans ) ($2.);

    run;quit;



