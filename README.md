# utl-rename-excel-columns-to-common-names-before-creating-sas-tables
Rename excel columns to common names before creating sas tables
    

    github
    https://tinyurl.com/yyfzxmtk
    https://github.com/rogerjdeangelis/utl-rename-excel-columns-to-common-names-before-creating-sas-tables


    SAS Forum
    https://communities.sas.com/t5/SAS-Programming/Help-to-rename-in-data-infile/m-p/560566

    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    * create two worksheets with diferent names;

    options validvarname=upcase;

    %utlfkil(d:/xls/have.xlsx);

    libname xel "d:/xls/have.xlsx";

    data xel.have1(rename=(sex=gender age=ageYrs))
         xel.have2(rename=name=firstname);

       set sashelp.class(keep=name sex age obs=3);

    run;quit;


    d:/xls/havea.xlsx

         +--------------------+---------+---------+
         |          A         |   B     |    C    |
         |--------------------+---------+---------+
      1  |     FIRSTNAME      |  SEX    |   AGE   |
         +--------------------+---------+---------+
      2  |       Alfred       |   14    |    M    |
         +---------------------+---------+--------+
      3  |       Alice        |   13    |    F    |
         +---------------------+---------+--------+
      4  |       Barbara      |   13    |    F    |
         +---------------------+---------+--------+
       [HAVEA]

    d:/xls/haveb.xlsx

         +--------------------+---------+---------+
         |          A         |   B     |    C    |
         |--------------------+---------+---------+
      1  |       NAME         | GENDER  | AGEYRS  |
         +--------------------+---------+---------+
      2  |       Alfred       |   14    |    M    |
         +---------------------+---------+--------+
      3  |       Alice        |   13    |    F    |
         +---------------------+---------+--------+
      4  |       Barbara      |   13    |    F    |
         +---------------------+---------+--------+
       [HAVEB]


    * rename mapping;
    * format with rename code;
    proc format;
       value $standardnames
       'AGE'         = 'AGE'
       'AGEYRS'      = 'AGE'
       'GENDER'      = 'SEX'
       'SEX'         = 'SEX'
       'FIRSTNAME'   = 'NAME'
       'NAME'        = 'NAME'
      ;
    run;quit;

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;


    WORK.HAVE1 total obs=3     RULE

       NAME      SEX    AGE    FIRSTNAME renamed to NAME

      Alfred      M      14
      Alice       F      13
      Barbara     F      13


    WORK.HAVE2 total obs=3     RULE

       NAME      SEX    AGE     GENDER renamed to SEX
                                AGEYRS renamed to AGE
      Alfred      M      14
      Alice       F      13
      Barbara     F      13


    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    * Create excel input sheets;

    options validvarname=upcase;

    %utlfkil(d:/xls/have.xlsx);

    libname xel "d:/xls/have.xlsx";

    data xel.have1(rename=(sex=gender age=ageYrs))
         xel.have2(rename=name=firstname);

       set sashelp.class(keep=name sex age obs=3);

    run;quit;

    * format with rename code;
    proc format;
       value $standardnames
       'AGE'         = 'AGE'
       'AGEYRS'      = 'AGE'
       'GENDER'      = 'SEX'
       'SEX'         = 'SEX'
       'FIRSTNAME'   = 'NAME'
       'NAME'        = 'NAME'
      ;
    run;quit;

    *          _       _   _                             _
     ___  ___ | |_   _| |_(_) ___  _ __     ___ ___   __| | ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \   / __/ _ \ / _` |/ _ \
    \__ \ (_) | | |_| | |_| | (_) | | | | | (_| (_) | (_| |  __/
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|  \___\___/ \__,_|\___|

    ;

    * get the names;
    %array(hava,values=%varlist(xel.have1));
    %array(havb,values=%varlist(xel.have2));

    * create SAS datasets from excel;
    data have1 ;
       set xel.have1;
       rename
        %do_over(hava,phrase=%nrstr(?=%sysfunc(putc(?,standardnames))))
       ;
    run;quit;

    data have2 ;
        set xel.have2;
        rename
           %do_over(havb,phrase=%nrstr(?=%sysfunc(putc(?,standardnames))))
        ;
    run;quit;

    libname xel clear;



