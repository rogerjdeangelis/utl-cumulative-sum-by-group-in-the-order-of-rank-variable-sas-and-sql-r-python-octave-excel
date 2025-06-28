# utl-cumulative-sum-by-group-in-the-order-of-rank-variable-sas-and-sql-r-python-octave-excel
Cumulative sum by group in the order of rank variable sas and sql r python octave excel
    %let pgm=utl-cumulative-sum-by-group-in-the-order-of-rank-variable-sas-and-sql-r-python-octave-excel;

    %stop_submission;

    Cumulative sum by group in the order of rank variable sas and sql r python octave excel

    PROBLEM (cumulate x and y in rnk order by f1, f2 combinations

              INPUT                           OUTPUT  =====        =====
         F1  F2 RNK X  Y        F1  F2 RNK    X       cum-x    Y   sum-y

         A   B   1  1  2        A   B   1     1         1      2    2
         A   B   2  2  4        A   B   2     2 1+2     3      4    6
         A   B   3  3  6        A   B   3     3 1+2+3   6      6   12

         A   C   1  1  7        A   C   1     1 1       1      7     7
         A   C   2  3  8        A   C   2     3 1+3     4      8    15
         A   C   3  5  9        A   C   3     5 1+3+5   9      9    24

    github
    https://tinyurl.com/yt5v5ca3
    https://github.com/rogerjdeangelis/utl-cumulative-sum-by-group-in-the-order-of-rank-variable-sas-and-sql-r-python-octave-excel

     CONTENTS

        1 base SAS
          Bartosz Jablonski
          yabwon@gmail.com
        2 matlab-octave sql (also supporst postgresSQL)
        3 r sql             (also supporst postgresSQL)
        4 python sql        (also supporst postgresSQL)
        5 excel sql
        7 spss via open source, pspp only supports postgreSQL
          (not included here)
          see https://tinyurl.com/5n94hdtp

    related
    SPSS via open source pspp
    https://tinyurl.com/5n94hdtp
    https://github.com/rogerjdeangelis/utl-dropping-down-to-spss-using-the-pspp-free-clone-and-running-a-spss-linear-regression

    communities.sas
    https://tinyurl.com/yyaat4rs
    https://communities.sas.com/t5/SAS-IML-Software-and-Matrix/Cumulative-sum-by-byvar-in-the-order-of-rank-variable/m-p/775629#M5711

    Related postgresSQL repos
    https://github.com/rogerjdeangelis/utl-loading-tiny-one-million-row-sas-dataset-into-postgres-db-sql-and-selecting-distinct-values
    https://github.com/rogerjdeangelis/utl-partial-key-matching-and-luminosity-in-gene-analysis-sas-r-python-postgresql
    https://github.com/rogerjdeangelis/utl-pivot-wide-when-variable-names-contain-values-sql-and-base-r-sas-oython-excel-postgreSQL
    https://github.com/rogerjdeangelis/utl-saving-and-creating-r-dataframes-to-and-from-a-postgresql-database-schema

    /***********************************************************************************************************************************/
    /*        INPUTS                 |   PROCESS                                                                                       */
    /*        ======                 |   ======                                                         |                              */
    /* SD1.have                      |  1 BASE SAS                                                      |             C  U             */
    /*  F1  F2 RNK X  Y              |  ==========                                                      |      R      M  M             */
    /*                               |  data want;                                                      | F F  N      _  _             */
    /*  A   B   1  1  2              |   set sd1.have;                                                  | 1 2  K  X Y X  Y             */
    /*  A   B   2  2  4              |   by F1 F2;                                                      |                              */
    /*  A   B   3  3  6              |    if first.F2 then                                              | A B  1  1 2 1  2             */
    /*  A   C   1  1  7              |     call missing(cum_x,cum_y);                                   | A B  2  2 4 3  6             */
    /*  A   C   2  3  8              |     cum_x + x;                                                   | A B  3  3 6 6 12             */
    /*  A   C   3  5  9              |     cum_y + y;                                                   | A C  1  1 7 1  7             */
    /*                               |  run;                                                            | A C  2  3 8 4 15             */
    /* FOR MATLAB                    |                                                                  | A C  3  5 9 9 24             */
    /* SQLITE TABLE HAVE             |  proc print data=want                                            |                              */
    /*                not            |     heading=vertical;                                            |                              */
    /* name type null dflt pk        |  run;quit;                                                       |                              */
    /*   F1 TEXT    0  NA  0         |                                                                  |                              */
    /*   F2 TEXT    0  NA  0         |-------------------------------------------------------------------------------------------------*/
    /*  RNK REAL    0  NA  0         |  2 MATLAB-OCTAVE                                                 |                              */
    /*    X REAL    0  NA  0         |  ==============                                                  |               cum cum        */
    /*    Y REAL    0  NA  0         |  %utl_mbegin;                                                    | F1 F2 RNK X Y x   y          */
    /*                               |  parmcards4;                                                     | __ __ ___ _ _ __ ___         */
    /* F1 F2 RNK X Y                 |  pkg load sqlite                                                 |                              */
    /*  A  B   1 1 2                 |  db = sqlite("d:/sqlite/have.db","create");                      | A  B  1   1 2 1  2           */
    /*  A  B   2 2 4                 |  execute(db, 'select load_extension("d:/dll/sqlean")');          | A  B  2   2 4 3  6           */
    /*  A  B   3 3 6                 |  meta = fetch(db, "pragma table_info('have');");                 | A  B  3   3 6 6  12          */
    /*  A  C   1 1 7                 |  disp(meta);                                                     | A  C  1   1 7 1  7           */
    /*  A  C   2 3 8                 |                                                                  | A  C  2   3 8 4  15          */
    /*  A  C   3 5 9                 |  want = fetch(db                                             ... | A  C  3   5 9 9  24          */
    /*                               |   ,[" select                                               " ... |                              */
    /*                               |     "   f1                                                 " ... |                              */
    /* options validvarname=upcase;  |     "  ,f2                                                 " ... |                              */
    /* libname sd1 "d:/sd1";         |     "  ,rnk                                                " ... |                              */
    /* data sd1.have;                |     "  ,x                                                  " ... |                              */
    /*  input f1 $ f2 $ rnk x y;     |     "  ,y                                                  " ... |                              */
    /* cards4;                       |     "  ,sum(x) over (                                      " ... |                              */
    /* A  B  1  1  2                 |     "     partition by f1, f2                              " ... |                              */
    /* A  B  2  2  4                 |     "     order by rnk                                     " ... |                              */
    /* A  B  3  3  6                 |     "     rows between unbounded preceding and current row " ... |                              */
    /* A  C  1  1  7                 |     "   ) as cum_x                                         " ... |                              */
    /* A  C  2  3  8                 |     "  ,sum(y) over (                                      " ... |                              */
    /* A  C  3  5  9                 |     "     partition by f1, f2                              " ... |                              */
    /* ;;;;                          |     "     order by rnk                                     " ... |                              */
    /* run;quit;                     |     "     rows between unbounded preceding and current row " ... |                              */
    /*                               |     "   ) as cum_y                                         " ... |                              */
    /*                               |     " from                                                 " ... |                              */
    /* %utlfkil("d:/sqlite/have.db");|     "   have;                                              " ... |                              */
    /*                               |     ]);                                                          |                              */
    /* %utl_rbeginx;                 |  disp(want)                                                      |                              */
    /* parmcards4;                   |  close(db);                                                      |                              */
    /* library(haven)                |  ;;;;                                                            |                              */
    /* library(DBI)                  |  %utl_mend;                                                      |                              */
    /* library(RSQLite)              |                                                                  |                              */
    /* have<-read_sas(               |                                                                  |                              */
    /*  "d:/sd1/have.sas7bdat")      |  3 r sql                                                         |                              */
    /* con <- dbConnect(             |                                                                  |                              */
    /*     RSQLite::SQLite()         |  proc datasets lib=sd1                                           |                              */
    /*    ,"d:/sqlite/have.db")      |     nolist nodetails;                                            |                              */
    /* dbWriteTable(                 |   delete want;                                                   |                              */
    /*     con                       |  run;quit;                                                       |                              */
    /*   ,"have"                     |                                                                  |                              */
    /*   ,have)                      |                                                                  |                              */
    /* dbListTables(con)             |-------------------------------------------------------------------------------------------------*/
    /*                               |  3 R SQL                                                         | > want;         cum cum      */
    /* dbGetQuery(                   |  =======                                                         |   F1 F2 RNK X Y   x    y     */
    /*    con                        |  %utl_rbeginx;                                                   | 1  A  B   1 1 2   1    2     */
    /*  ,"SELECT                     |  parmcards4;                                                     | 2  A  B   2 2 4   3    6     */
    /*      *                        |  library(haven)                                                  | 3  A  B   3 3 6   6   12     */
    /*    FROM                       |  library(sqldf)                                                  | 4  A  C   1 1 7   1    7     */
    /*      have")                   |  source("c:/oto/fn_tosas9x.R")                                   | 5  A  C   2 3 8   4   15     */
    /* dbGetQuery(con                |  options(sqldf.dll="d:/dll/sqlean.dll")                          | 6  A  C   3 5 9   9   24     */
    /* ,"SELECT                      |  have<-read_sas("d:/sd1/have.sas7bdat")                          |                              */
    /*     *                         |  print(have)                                                     |                              */
    /*  FROM                         |  want<-sqldf('                                                   | SAS                          */
    /*   pragma_table_info('have')") |   select                                                         |                 cum  cum     */
    /* dbDisconnect(con)             |     f1                                                           | F1 F2 RNK  X  Y  x    y      */
    /* ;;;;                          |    ,f2                                                           |                              */
    /* %utl_rendx;                   |    ,rnk                                                          | A  B  1    1  2  1    2      */
    /*                               |    ,x                                                            | A  B  2    2  4  3    6      */
    /*                               |    ,y                                                            | A  B  3    3  6  6    12     */
    /*                               |    ,sum(x) over (                                                | A  C  1    1  7  1    7      */
    /*                               |       partition by f1, f2                                        | A  C  2    3  8  4    15     */
    /*                               |       order by rnk                                               | A  C  3    5  9  9    24     */
    /*                               |       rows between unbounded preceding and current row           |                              */
    /*                               |     ) as cum_x                                                   |                              */
    /*                               |    ,sum(y) over (                                                |                              */
    /*                               |       partition by f1, f2                                        |                              */
    /*                               |       order by rnk                                               |                              */
    /*                               |       rows between unbounded preceding and current row           |                              */
    /*                               |     ) as cum_y                                                   |                              */
    /*                               |   from                                                           |                              */
    /*                               |     have;                                                        |                              */
    /*                               |   ')                                                             |                              */
    /*                               |  want;                                                           |                              */
    /*                               |  fn_tosas9x(                                                     |                              */
    /*                               |        inp    = want                                             |                              */
    /*                               |       ,outlib ="d:/sd1/"                                         |                              */
    /*                               |       ,outdsn ="want"                                            |                              */
    /*                               |       )                                                          |                              */
    /*                               |  ;;;;                                                            |                              */
    /*                               |  %utl_rendx;                                                     |                              */
    /*                               |                                                                  |                              */
    /*                               |  proc print data=sd1.want headings=vertical;                     |                              */
    /*                               |  run;quit;                                                       |                              */
    /*                               |-------------------------------------------------------------------------------------------------*/
    /*                               |                                                                  |                  cum  cum    */
    /*                               |  4 PYTHON SQL                                                    |   F1 F2 RNK X  Y  x    y     */
    /*                               |  ============                                                    | 0  A  B  1  1  2  1    2     */
    /*                               |                                                                  | 1  A  B  2  2  4  3    6     */
    /*                               |  proc datasets lib=sd1 nolist nodetails;                         | 2  A  B  3  3  6  6 1  2     */
    /*                               |   delete pywant;                                                 | 3  A  C  1  1  7  1    7     */
    /*                               |  run;quit;                                                       | 4  A  C  2  3  8  4 1  5     */
    /*                               |                                                                  | 5  A  C  3  5  9  9 2  4     */
    /*                               |  %utl_pybeginx;                                                  |                              */
    /*                               |  parmcards4;                                                     |                              */
    /*                               |  exec(open('c:/oto/fn_pythonx.py').read());                      |                              */
    /*                               |  have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');           |                              */
    /*                               |  want=pdsql('''                                                  | SAS                          */
    /*                               |   select                                                         |                 cum  cum     */
    /*                               |     f1                                                           | F1 F2 RNK  X  Y  x    y      */
    /*                               |    ,f2                                                           |                              */
    /*                               |    ,rnk                                                          | A  B  1    1  2  1    2      */
    /*                               |    ,x                                                            | A  B  2    2  4  3    6      */
    /*                               |    ,y                                                            | A  B  3    3  6  6    12     */
    /*                               |    ,sum(x) over (                                                | A  C  1    1  7  1    7      */
    /*                               |       partition by f1, f2                                        | A  C  2    3  8  4    15     */
    /*                               |       order by rnk                                               | A  C  3    5  9  9    24     */
    /*                               |       rows between unbounded preceding and current row           |                              */
    /*                               |     ) as cum_x                                                   |                              */
    /*                               |    ,sum(y) over (                                                |                              */
    /*                               |       partition by f1, f2                                        |                              */
    /*                               |       order by rnk                                               |                              */
    /*                               |       rows between unbounded preceding and current row           |                              */
    /*                               |     ) as cum_y                                                   |                              */
    /*                               |   from                                                           |                              */
    /*                               |     have ''')                                                    |                              */
    /*                               |  print(want);                                                    |                              */
    /*                               |  fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);    |                              */
    /*                               |  ;;;;                                                            |                              */
    /*                               |  %utl_pyendx;                                                    |                              */
    /*                               |                                                                  |                              */
    /*                               |  proc print data=sd1.pywant;                                     |                              */
    /*                               |  run;quit;                                                       |                              */
    /*                               |                                                                  |                              */
    /*                               |-------------------------------------------------------------------------------------------------*/
    /*                               |  5 EXCEL SQL                                                     | -------------------+         */
    /*                               |  ===========                                                     | | A1  |fx    | A   |         */
    /*                               |                                                                  | -------------------------+   */
    /*                               |  proc datasets lib=sd1                                           | [_]|A |B |   |D|E| F | G |   */
    /*                               |     nolist nodetails;                                            | -------------------------|   */
    /*                               |   delete want;                                                   |  1 |F1|F2|rnk|X|Y|cum|cum|   */
    /*                               |  run;quit;                                                       |    |  |  |   | | |X  |Y  |   */
    /*                               |                                                                  |  --|--+--+---+-+-+---+---|   */
    /*                               |  %utlfkil(d:/xls/wantxl.xlsx);                                   |  2 | A|B | 1 |1|2|1  | 2 |   */
    /*                               |                                                                  |  --|--+--+---+-+-+---+---|   */
    /*                               |  %utl_rbeginx;                                                   |  3 | A|B | 2 |2|4|3  | 6 |   */
    /*                               |  parmcards4;                                                     |  --|--+--+---+-+-+---+---|   */
    /*                               |  library(openxlsx)                                               |  4 | A|B | 3 |3|6|6  | 12|   */
    /*                               |  library(sqldf)                                                  |  --|--+--+---+-+-+---+---|   */
    /*                               |  library(haven)                                                  |  5 | A|C | 1 |1|7|1  | 7 |   */
    /*                               |  have<-read_sas("d:/sd1/have.sas7bdat")                          |  --|--+--+---+-+-+---+---|   */
    /*                               |  wb <- createWorkbook()                                          |  6 | A|C | 2 |3|8|4  | 15|   */
    /*                               |  addWorksheet(wb, "have")                                        |  --|--+--+---+-+-+---+---|   */
    /*                               |  writeData(wb, sheet = "have", x = have)                         |  7 | A|C | 3 |5|9|9  | 24|   */
    /*                               |  saveWorkbook(                                                   |  --|--+--+---+-+-+---+---|   */
    /*                               |      wb                                                          |  ]WANT]                      */
    /*                               |     ,"d:/xls/wantxl.xlsx"                                        |                              */
    /*                               |     ,overwrite=TRUE)                                             |                              */
    /*                               |  ;;;;                                                            |                              */
    /*                               |  %utl_rendx;                                                     |                              */
    /*                               |                                                                  |                              */
    /*                               |  %utl_rbeginx;                                                   |                              */
    /*                               |  parmcards4;                                                     |                              */
    /*                               |  library(openxlsx)                                               |                              */
    /*                               |  library(sqldf)                                                  |                              */
    /*                               |  source("c:/oto/fn_tosas9x.R")                                   |                              */
    /*                               |   wb<-loadWorkbook("d:/xls/wantxl.xlsx")                         |                              */
    /*                               |   have <-read.xlsx(wb,"have")                                    |                              */
    /*                               |   addWorksheet(wb, "want")                                       |                              */
    /*                               |   want <- sqldf('                                                |                              */
    /*                               |    select                                                        |                              */
    /*                               |      f1                                                          |                              */
    /*                               |     ,f2                                                          |                              */
    /*                               |     ,rnk                                                         |                              */
    /*                               |     ,x                                                           |                              */
    /*                               |     ,y                                                           |                              */
    /*                               |     ,sum(x) over (                                               |                              */
    /*                               |        partition by f1, f2                                       |                              */
    /*                               |        order by rnk                                              |                              */
    /*                               |        rows between unbounded preceding and current row          |                              */
    /*                               |      ) as cum_x                                                  |                              */
    /*                               |     ,sum(y) over (                                               |                              */
    /*                               |        partition by f1, f2                                       |                              */
    /*                               |        order by rnk                                              |                              */
    /*                               |        rows between unbounded preceding and current row          |                              */
    /*                               |      ) as cum_y                                                  |                              */
    /*                               |    from                                                          |                              */
    /*                               |      have;                                                       |                              */
    /*                               |    ')                                                            |                              */
    /*                               |   print(want)                                                    |                              */
    /*                               |   writeData(wb,sheet="want",x=want)                              |                              */
    /*                               |   saveWorkbook(                                                  |                              */
    /*                               |       wb                                                         |                              */
    /*                               |      ,"d:/xls/wantxl.xlsx"                                       |                              */
    /*                               |      ,overwrite=TRUE)                                            |                              */
    /*                               |  fn_tosas9x(                                                     |                              */
    /*                               |        inp    = want                                             |                              */
    /*                               |       ,outlib ="d:/sd1/"                                         |                              */
    /*                               |       ,outdsn ="want"                                            |                              */
    /*                               |       )                                                          |                              */
    /*                               |  ;;;;                                                            |                              */
    /*                               |  %utl_rendx;                                                     |                              */
    /*                               |                                                                  |                              */
    /*                               |  proc print data=sd1.want;                                       |                              */
    /*                               |  run;quit;                                                       |                              */
    /***********************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
     input f1 $ f2 $ rnk x y;
    cards4;
    A  B  1  1  2
    A  B  2  2  4
    A  B  3  3  6
    A  C  1  1  7
    A  C  2  3  8
    A  C  3  5  9
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*  sas dataset SD1.have                                                                                                  */
    /*   F1  F2 RNK X  Y                                                                                                      */
    /*                                                                                                                        */
    /*   A   B   1  1  2                                                                                                      */
    /*   A   B   2  2  4                                                                                                      */
    /*   A   B   3  3  6                                                                                                      */
    /*   A   C   1  1  7                                                                                                      */
    /*   A   C   2  3  8                                                                                                      */
    /*   A   C   3  5  9                                                                                                      */
    /**************************************************************************************************************************/

    %utlfkil("d:/sqlite/have.db");

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(DBI)
    library(RSQLite)
    have<-read_sas(
     "d:/sd1/have.sas7bdat")
    con <- dbConnect(
        RSQLite::SQLite()
       ,"d:/sqlite/have.db")
    dbWriteTable(
        con
      ,"have"
      ,have)
    dbListTables(con)

    dbGetQuery(
       con
     ,"SELECT
         *
       FROM
         have")
    dbGetQuery(con
    ,"SELECT
        *
     FROM
      pragma_table_info('have')")
    dbDisconnect(con)
    ;;;;
    %utl_rendx;

    /**************************************************************************************************************************/
    /* FOR MATLAB                                                                                                             */
    /* SQLITE TABLE HAVE                                                                                                      */
    /*                not                                                                                                     */
    /* name type null dflt pk                                                                                                 */
    /*   F1 TEXT    0  NA  0                                                                                                  */
    /*   F2 TEXT    0  NA  0                                                                                                  */
    /*  RNK REAL    0  NA  0                                                                                                  */
    /*    X REAL    0  NA  0                                                                                                  */
    /*    Y REAL    0  NA  0                                                                                                  */
    /*                                                                                                                        */
    /* F1 F2 RNK X Y                                                                                                          */
    /*  A  B   1 1 2                                                                                                          */
    /*  A  B   2 2 4                                                                                                          */
    /*  A  B   3 3 6                                                                                                          */
    /*  A  C   1 1 7                                                                                                          */
    /*  A  C   2 3 8                                                                                                          */
    /*  A  C   3 5 9                                                                                                          */
    /**************************************************************************************************************************/

    /*   _
    / | | |__   __ _ ___  ___   ___  __ _ ___
    | | | `_ \ / _` / __|/ _ \ / __|/ _` / __|
    | | | |_) | (_| \__ \  __/ \__ \ (_| \__ \
    |_| |_.__/ \__,_|___/\___| |___/\__,_|___/

    1 base SAS

    */

    data want;
     set sd1.have;
     by F1 F2;
      if first.F2 then
       call missing(cum_x,cum_y)
       cum_x + x;
       cum_y + y;
    run;

    proc print data=want;
    run;quit;

    /**************************************************************************************************************************/
    /* work.want                                                                                                              */
    /*  F1    F2    RNK    X    Y    CUM_X    CUM_Y                                                                           */
    /*                                                                                                                        */
    /*  A     B      1     1    2      1         2                                                                            */
    /*  A     B      2     2    4      3         6                                                                            */
    /*  A     B      3     3    6      6        12                                                                            */
    /*  A     C      1     1    7      1         7                                                                            */
    /*  A     C      2     3    8      4        15                                                                            */
    /*  A     C      3     5    9      9        24                                                                            */
    /**************************************************************************************************************************/

    /*___                    _   _       _                   _                             _
    |___ \   _ __ ___   __ _| |_| | __ _| |__      ___   ___| |_ __ ___   _____  ___  __ _| |
      __) | | `_ ` _ \ / _` | __| |/ _` | `_ \ __ / _ \ / __| __/ _` \ \ / / _ \/ __|/ _` | |
     / __/  | | | | | | (_| | |_| | (_| | |_) |__| (_) | (__| || (_| |\ V /  __/\__ \ (_| | |
    |_____| |_| |_| |_|\__,_|\__|_|\__,_|_.__/    \___/ \___|\__\__,_| \_/ \___||___/\__, |_|
                                                                                        |_|
    2 matlab-octave sql
    */

    %utl_mbegin;
    parmcards4;
    pkg load sqlite
    db = sqlite("d:/sqlite/have.db","create");
    execute(db, 'select load_extension("d:/dll/sqlean")');
    meta = fetch(db, "pragma table_info('have');");
    disp(meta);
    want = fetch(db                                             ...
     ,[" select                                               " ...
       "   f1                                                 " ...
       "  ,f2                                                 " ...
       "  ,rnk                                                " ...
       "  ,x                                                  " ...
       "  ,y                                                  " ...
       "  ,sum(x) over (                                      " ...
       "     partition by f1, f2                              " ...
       "     order by rnk                                     " ...
       "     rows between unbounded preceding and current row " ...
       "   ) as cum_x                                         " ...
       "  ,sum(y) over (                                      " ...
       "     partition by f1, f2                              " ...
       "     order by rnk                                     " ...
       "     rows between unbounded preceding and current row " ...
       "   ) as cum_y                                         " ...
       " from                                                 " ...
       "   have;                                              " ...
       ]);
    disp(want)
    whos want
    close(db);
    ;;;;
    %utl_mend;


    3 r sql

    proc datasets lib=sd1
       nolist nodetails;
     delete want;
    run;quit;

    /**************************************************************************************************************************/
    /* INPUT TABLE HAVE                            |  OUTPUT                                                                  */
    /*   cid  name  type  notnull  dflt_value  pk  |  F1  F2  RNK  X  Y  cum_x  cum_y                                         */
    /*   ___  ____  ____  _______  __________  __  |  __  __  ___  _  _  _____  _____                                         */
    /*                                             |                                                                          */
    /*   0    F1    TEXT  0                    0   |  A   B   1    1  2  1      2                                             */
    /*   1    F2    TEXT  0                    0   |  A   B   2    2  4  3      6                                             */
    /*   2    RNK   REAL  0                    0   |  A   B   3    3  6  6      12                                            */
    /*   3    X     REAL  0                    0   |  A   C   1    1  7  1      7                                             */
    /*   4    Y     REAL  0                    0   |  A   C   2    3  8  4      15                                            */
    /*                                             |  A   C   3    5  9  9      24                                            */
    /*                                             |                                                                          */
    /*                                             |                                                                          */
    /*                                             | Attr   Name        Size    Bytes  Class                                  */
    /*                                             | ====   ====        ====    =====  =====                                  */
    /*                                             |        want        6x7         0  dbtable --> may want to change         */
    /**************************************************************************************************************************/

    /*____                    _
    |___ /   _ __   ___  __ _| |
      |_ \  | `__| / __|/ _` | |
     ___) | | |    \__ \ (_| | |
    |____/  |_|    |___/\__, |_|
                           |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    options(sqldf.dll="d:/dll/sqlean.dll")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want<-sqldf('
     select
       f1
      ,f2
      ,rnk
      ,x
      ,y
      ,sum(x) over (
         partition by f1, f2
         order by rnk
         rows between unbounded preceding and current row
       ) as cum_x
      ,sum(y) over (
         partition by f1, f2
         order by rnk
         rows between unbounded preceding and current row
       ) as cum_y
     from
       have;
     ')
    want;
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want ;
    run;quit;

    /**************************************************************************************************************************/
    /* R                            | SAS                                                                                     */
    /*   F1 F2 RNK X Y cum_x cum_y  |  F1    F2    RNK    X    Y    CUM_X    CUM_Y                                            */
    /*                              |                                                                                         */
    /* 1  A  B   1 1 2     1     2  |  A     B      1     1    2      1         2                                             */
    /* 2  A  B   2 2 4     3     6  |  A     B      2     2    4      3         6                                             */
    /* 3  A  B   3 3 6     6    12  |  A     B      3     3    6      6        12                                             */
    /* 4  A  C   1 1 7     1     7  |  A     C      1     1    7      1         7                                             */
    /* 5  A  C   2 3 8     4    15  |  A     C      2     3    8      4        15                                             */
    /* 6  A  C   3 5 9     9    24  |  A     C      3     5    9      9        24                                             */
    /**************************************************************************************************************************/

    /*  _                 _   _                             _
    | || |    _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    | || |_  | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
    |__   _| | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
       |_|   | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
             |_|    |___/                                |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_pythonx.py').read());
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    want=pdsql('''
     select
       f1
      ,f2
      ,rnk
      ,x
      ,y
      ,sum(x) over (
         partition by f1, f2
         order by rnk
         rows between unbounded preceding and current row
       ) as cum_x
      ,sum(y) over (
         partition by f1, f2
         order by rnk
         rows between unbounded preceding and current row
       ) as cum_y
     from
       have ''')
    print(want);
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx;

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /* PYTHON                                 | SAS                                                                           */
    /*    F1 F2  RNK    X    Y  cum_x  cum_y  | F1    F2    RNK    X    Y    CUM_X    CUM_Y                                   */
    /*                                        |                                                                               */
    /*  0  A  B  1.0  1.0  2.0    1.0    2.0  | A     B      1     1    2      1         2                                    */
    /*  1  A  B  2.0  2.0  4.0    3.0    6.0  | A     B      2     2    4      3         6                                    */
    /*  2  A  B  3.0  3.0  6.0    6.0   12.0  | A     B      3     3    6      6        12                                    */
    /*  3  A  C  1.0  1.0  7.0    1.0    7.0  | A     C      1     1    7      1         7                                    */
    /*  4  A  C  2.0  3.0  8.0    4.0   15.0  | A     C      2     3    8      4        15                                    */
    /*  5  A  C  3.0  5.0  9.0    9.0   24.0  | A     C      3     5    9      9        24                                    */
    /**************************************************************************************************************************/

    /*___                      _             _
    | ___|    _____  _____ ___| |  ___  __ _| |
    |___ \   / _ \ \/ / __/ _ \ | / __|/ _` | |
     ___) | |  __/>  < (_|  __/ | \__ \ (_| | |
    |____/   \___/_/\_\___\___|_| |___/\__, |_|
                                          |_|
    */

    5 EXCEL SQL
    ===========

    proc datasets lib=sd1
       nolist nodetails;
     delete want;
    run;quit;

    %utlfkil(d:/xls/wantxl.xlsx);

    %utl_rbeginx;
    parmcards4;
    library(openxlsx)
    library(sqldf)
    library(haven)
    have<-read_sas("d:/sd1/have.sas7bdat")
    wb <- createWorkbook()
    addWorksheet(wb, "have")
    writeData(wb, sheet = "have", x = have)
    saveWorkbook(
        wb
       ,"d:/xls/wantxl.xlsx"
       ,overwrite=TRUE)
    ;;;;
    %utl_rendx;

    %utl_rbeginx;
    parmcards4;
    library(openxlsx)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
     wb<-loadWorkbook("d:/xls/wantxl.xlsx")
     have <-read.xlsx(wb,"have")
     addWorksheet(wb, "want")
     want <- sqldf('
      select
        f1
       ,f2
       ,rnk
       ,x
       ,y
       ,sum(x) over (
          partition by f1, f2
          order by rnk
          rows between unbounded preceding and current row
        ) as cum_x
       ,sum(y) over (
          partition by f1, f2
          order by rnk
          rows between unbounded preceding and current row
        ) as cum_y
      from
        have;
      ')
     print(want)
     writeData(wb,sheet="want",x=want)
     saveWorkbook(
         wb
        ,"d:/xls/wantxl.xlsx"
        ,overwrite=TRUE)
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;


    /**************************************************************************************************************************/
    /*  -------------------+                                                                                                  */
    /*  | A1  |fx    | A   |                                                                                                  */
    /*  -------------------------+                                                                                            */
    /*  [_]|A |B |   |D|E| F | G |                                                                                            */
    /*  -------------------------|                                                                                            */
    /*   1 |F1|F2|rnk|X|Y|cum|cum|                                                                                            */
    /*     |  |  |   | | |X  |Y  |                                                                                            */
    /*   --|--+--+---+-+-+---+---|                                                                                            */
    /*   2 | A|B | 1 |1|2|1  | 2 |                                                                                            */
    /*   --|--+--+---+-+-+---+---|                                                                                            */
    /*   3 | A|B | 2 |2|4|3  | 6 |                                                                                            */
    /*   --|--+--+---+-+-+---+---|                                                                                            */
    /*   4 | A|B | 3 |3|6|6  | 12|                                                                                            */
    /*   --|--+--+---+-+-+---+---|                                                                                            */
    /*   5 | A|C | 1 |1|7|1  | 7 |                                                                                            */
    /*   --|--+--+---+-+-+---+---|                                                                                            */
    /*   6 | A|C | 2 |3|8|4  | 15|                                                                                            */
    /*   --|--+--+---+-+-+---+---|                                                                                            */
    /*   7 | A|C | 3 |5|9|9  | 24|                                                                                            */
    /*   --|--+--+---+-+-+---+---|                                                                                            */
    /*  [WANT]                                                                                                                */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
