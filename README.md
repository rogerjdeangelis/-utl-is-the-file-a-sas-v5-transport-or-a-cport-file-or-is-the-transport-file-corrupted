# utl-is-the-file-a-sas-v5-transport-or-a-cport-file-or-is-the-transport-file-corrupted
Is the file a sas v5 transport, cport or is the transport file corrupted


    /*
    (_)_ __    _ __  _ __ ___   __ _ _ __ ___  ___ ___
    | | `_ \  | `_ \| `__/ _ \ / _` | `__/ _ \/ __/ __|
    | | | | | | |_) | | | (_) | (_| | | |  __/\__ \__ \
    |_|_| |_| | .__/|_|  \___/ \__, |_|  \___||___/___/
              |_|              |___/
    */

    Is the file a sas v5 transport or a cport file or is the transport file corrupted

    I will focus on analysis of sas transort file without sas;

    Notes:
      1. It is now possible to convert a v5 sas transport file into ito R dataframes, sas datasets,
         or panda dataframes without a sas license using Python or or R.
      2. SAS V5 transport files can be opened in the SAS universal viewer. ( used to be able to save as csvs)
         I may have a download of the original SAS universal viewer that supports conversion to csvs?
      3. Python pyreadstat seems to have issues importing v5 transport files

      You will need to change the paths in some of the code below.
      Look for c:/xpt

     Below is my experience may not be accurate.

     Free software solutions ( https://dullesresearch.com/  has very low cost and free full function sas writers)
     I am in the process of puchasing the $195 desktop R and Python sas writer.
     I have no affiliation with DullesReaserch.


      Capabilities for v5 transport
      =============================
                                  R     Python    Limits
                                 ===    ======
      read multiple entries      Yes    No        200 byte characters and 8 character variable names
      read if just 1 entry        Yes   Yes       200 byte characters and 8 character variable names
                                                  Pyhton can only handle a v5 transport with one entry?

      Capabilities for sas7bdats
      =============================

      Read sas7bdats             Yes    Yes       Not sure they can read a sas dataset over 2gb
      Write sas7bdats            Yes    Yes*      *Document indicates Python with S-JDBC supports this. I have not
                                                  tried it yet. Seems to work only with insert statements?
                                                  Better to create R dataframes in Python then run R to create sa7bdat.
                                                  Python limits char variables to 255 bytes?

      read SAS formats           Maybe  Yes       Use cntlout on proc format?
                                                  Have had better luck with python
                                                  Limited luck with R
      Capabilities for sas cport files
      ================================

      Read cport                  no     no       No support in R or Python
      Write cport                 no     no       No support in R or Python


    All the macro sas code below is located at  'https://tinyurl.com/y9nfugth'
    summary(lm(Sepal.Length ~ Sepal.Width, data=iris))

    *    _            __ _     _           _  __       _
      __| |_ __ __ _ / _| |_  | |__   __ _| |/ _|   __| | ___  _ __   ___
     / _` | '__/ _` | |_| __| | '_ \ / _` | | |_   / _` |/ _ \| '_ \ / _ \
    | (_| | | | (_| |  _| |_  | | | | (_| | |  _| | (_| | (_) | | | |  __/
     \__,_|_|  \__,_|_|  \__| |_| |_|\__,_|_|_|    \__,_|\___/|_| |_|\___|
    ;
    *                _             _
      ___ ___  _ __ | |_ ___ _ __ | |_ ___
     / __/ _ \| '_ \| __/ _ \ '_ \| __/ __|
    | (_| (_) | | | | ||  __/ | | | |_\__ \
     \___\___/|_| |_|\__\___|_| |_|\__|___/
         _           ____               _
        / |   __   _| ___|  __  ___ __ | |_
        | |   \ \ / /___ \  \ \/ / '_ \| __|
        | |_   \ V / ___) |  >  <| |_) | |_
        |_(_)   \_/ |____/  /_/\_\ .__/ \__|
                                 |_|

           1. V5 transport file using proc copy
             R
              a. creating v5 transport file using sas proc copy with three entries.
              b. If you cannot create the v5 transport file you can download it form GitHub.
              c. Header meta data in the v5 transport file.
              d. Contents of corrupt v5 transport file
              e. Reading the v5 transport file using R and creating R and  dataframes and SAS datasets
              f. importing sas v5 transport file using sas
              g. importing sas v5 transport file using Python.
                 (unlike R it appears that python can only input v5 transpots that contan 1 sas dataset)
         ____                            _
        |___ \      ___ _ __   ___  _ __| |_
          __) |    / __| '_ \ / _ \| '__| __|
         / __/ _  | (__| |_) | (_) | |  | |_
        |_____(_)  \___| .__/ \___/|_|   \__|
                       |_|

              a. creating cport file with with 3 entries
              b. If you cannot create the cport transport file you can download it form GitHub.
              c. Header meta data in the vcport transport file (using the default compression).
              d. Contents of corrupt cport file
              e. Importing sas cport file using sas

    *_          ____               _
    / |  __   _| ___|  __  ___ __ | |_
    | |  \ \ / /___ \  \ \/ / '_ \| __|
    | |_  \ V / ___) |  >  <| |_) | |_
    |_(_)  \_/ |____/  /_/\_\ .__/ \__|
      __ _     _ __ ___   __|_| | _____  __  ___ __ | |_
     / _` |   | '_ ` _ \ / _` | |/ / _ \ \ \/ / '_ \| __|
    | (_| |_  | | | | | | (_| |   <  __/  >  <| |_) | |_
     \__,_(_) |_| |_| |_|\__,_|_|\_\___| /_/\_\ .__/ \__|
    ;                                         |_|

    * create a sas v5 export file containing 3 sas datasets;
    * if you do not have sas use the code below to download from github;

    options obs=30;
    LIBNAME sasxpt XPORT "c:/xpt/sasxpt.xpt";
    proc copy in=sashelp out=sasxpt;
      select class prdsale prdsal2;
    run;quit;

    * It is nice that you can run proc contents on a V5 transport without importing or use the
    sas universal viewer on the file d:/xpt/sasxpt.xpt

    proc contents data=sasxpt._all_;
    run;quit;

    /*
    Libref         SASXPT
    Engine         XPORT
    Physical Name  d:\xpt\sasxpt.xpt


                Member  Obs, Entries
    #  Name     Type     or Indexes   Vars  Label

    1  CLASS    DATA         .          5   Student Data
    2  PRDSALE  DATA         .         10   Furniture sales data
    3  PRDSAL2  DATA         .         11   Furniture sales data
    */

    *_            _                     _                 _              _
    | |__      __| | _____      ___ __ | | ___   __ _  __| | __  ___ __ | |_
    | '_ \    / _` |/ _ \ \ /\ / / '_ \| |/ _ \ / _` |/ _` | \ \/ / '_ \| __|
    | |_) |  | (_| | (_) \ V  V /| | | | | (_) | (_| | (_| |  >  <| |_) | |_
    |_.__(_)  \__,_|\___/ \_/\_/ |_| |_|_|\___/ \__,_|\__,_| /_/\_\ .__/ \__|
                                                                  |_|
    ;
    * If you cannot create the sas transport datasets using the sas
      code above you can use the R code below to download and decode code;
    * Note GitHub does not support binary downloads
      so I have created a base64 encoded version on the binary v5 transport file;
    * The encoded version at https://tinyurl.com/2cpwhja6 can be decoded into
      the binary v5 transport file sasxpt.xpt;

    * binary download of the v5 transport file;
    %utl_submit_r64('
    url<-"https://tinyurl.com/2cpwhja6";
    xpt="c:/xpt/sasxpt.xpt";
    if (file.exists(xpt)) { file.remove(xpt) };
    tmp<-tempfile();
    download.file(url, tmp);
    base64::decode(tmp, xpt);
    ');

    *                    _     _                    _
      ___    __  ___ __ | |_  | |__   ___  __ _  __| | ___ _ __
     / __|   \ \/ / '_ \| __| | '_ \ / _ \/ _` |/ _` |/ _ \ '__|
    | (__ _   >  <| |_) | |_  | | | |  __/ (_| | (_| |  __/ |
     \___(_) /_/\_\ .__/ \__| |_| |_|\___|\__,_|\__,_|\___|_|
                  |_|
    ;

    CONTETS OF HEADER;

        1. Header consists of 80 byte records that are back to back.
           Records are not separted by a cariage-return-line ie cr/lf '0D0A'x.
        2. It is a v5 transport because of first record
           'HEADER RECORD*******LIBRARY HEADER RECORD!!!!!!!000000000000000000000000000000'
        3. Created by 32 bit SAS on Win 10 because 'W32_10PR'
        4. Created on '27MAR21:08:41:06
        5. First dataset is class.sas7bdat
        6. Type 'data' created by SAS 9.4
           'CLASS   SASDATA 9.4'

    * Macro utlhex created this output. Macro on end and in  'https://tinyurl.com/y9nfugth'.

    %utlhex(uinflt =c:/xpt/sasxpt.xpt,uotflt =c:/xpt/sasxpt.txt);

    Input  d:/xpt/sasxpt.xpt
    output d:/xpt/sasxpt.txt


     --- Record Number ---  1   ---  Record Length ---- 80

    HEADER RECORD*******LIBRARY HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444545524444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAC92212908514520253F24111111100000000000000000000000000000000


     --- Record Number ---  2   ---  Record Length ---- 80

    SAS     SAS     SASLIB  9.4     W32_10PR                        27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    54522222545222225454442232322222533533552222222222222222222222223344533333333333
    3130000031300000313C92009E400000732F100200000000000000000000000027D1221A08A41A06


     --- Record Number ---  3   ---  Record Length ---- 80

    27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    33445333333333332222222222222222222222222222222222222222222222222222222222222222
    27D1221A08A41A060000000000000000000000000000000000000000000000000000000000000000


     --- Record Number ---  4   ---  Record Length ---- 80

    HEADER RECORD*******MEMBER  HEADER RECORD!!!!!!!000000000000000001600000000140
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444445224444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAD5D252008514520253F24111111100000000000000000160000000014000


     --- Record Number ---  5   ---  Record Length ---- 80

    HEADER RECORD*******DSCRPTR HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222454555524444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAA433204208514520253F24111111100000000000000000000000000000000


     --- Record Number ---  6   ---  Record Length ---- 80

    SAS     CLASS   SASDATA 9.4     W32_10PR                        27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    54522222444552225454454232322222533533552222222222222222222222223344533333333333
    313000003C133000313414109E400000732F100200000000000000000000000027D1221A08A41A06

    *                                         _
      ___      ___ ___  _ __ _ __ _   _ _ __ | |_  __  ___ __ | |_
     / __|    / __/ _ \| '__| '__| | | | '_ \| __| \ \/ / '_ \| __|
    | (__ _  | (_| (_) | |  | |  | |_| | |_) | |_   >  <| |_) | |_
     \___(_)  \___\___/|_|  |_|   \__,_| .__/ \__| /_/\_\ .__/ \__|
                                       |_|              |_|
    ;

    * corrupted because non-binary upload or download;
    * import corrupted v5 transport;

    %utl_submit_r64('
    url<-"https://tinyurl.com/4khm4pjm";
    xpt="c:/xpt/sasv5corrupt.xpt";
    if (file.exists(xpt)) { file.remove(xpt) };
    tmp<-tempfile();
    download.file(url, tmp);
    base64::decode(tmp, xpt);
    ');

    * Try to import v5 xpt into R

    %utl_submit_r64('
     library(SASxport);
     lookup.xport("d:/xpt/sasv5corrupt.xpt");
    ');

    * PROBLEM WITH HEADER;

    Stderr output:
    Error in lookup.xport.inner(file) :
      SAS transfer file has incorrect library header
    Calls: lookup.xport -> lookup.xport.inner

    * Macro utlhex created this output. Macro on end and in  'https://tinyurl.com/y9nfugth';.
    %utlhex(uinflt =c:/xpt/sasv5corrupt.xpt,uotflt =c:/xpt/sasv5corrupt.txt);


     Issues with the corrupt v5 xport file

      1. The header records are not on 80 byte boundaries.

         Record 4 is offset by 6 bytes because three CR/LFs have been inserted.

         --- Record Number ---  4   ---  Record Length ---- 80
             ..HEADER RECORD*******MEMBER  HEADER RECORD!!!!!!!00000000000000000160000000
         1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8

      2. We have CR/LFa '0D0A'x in the data. Where they should not be.

          --- Record Number ---  2 hav 'OD0A'x in bytes 1 & 2
         ..
         1.
         00
         DA


    d:/xpt/sasv5corrupt.xpt
    d:/xpt/sasv5corrupt.txt


     --- Record Number ---  1   ---  Record Length ---- 80

    HEADER RECORD*******LIBRARY HEADER RECORD!!!!!!!000000000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44444525444542222222444545524444452544454222222233333333333333333333333333333322
    8514520253F24AAAAAAAC92212908514520253F24111111100000000000000000000000000000000


     --- Record Number ---  2   ---  Record Length ---- 80

    ..SAS     SAS     SASLIB  9.4     W32_10PR                        27MAR21:08:41:
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    00545222225452222254544422323222225335335522222222222222222222222233445333333333
    DA3130000031300000313C92009E400000732F100200000000000000000000000027D1221A08A41A


     --- Record Number ---  3   ---  Record Length ---- 80

    06..27MAR21:08:41:06
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    33003344533333333333222222222222222222222222222222222222222222222222222222222222
    06DA27D1221A08A41A06000000000000000000000000000000000000000000000000000000000000


     --- Record Number ---  4   ---  Record Length ---- 80

        ..HEADER RECORD*******MEMBER  HEADER RECORD!!!!!!!00000000000000000160000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    22220044444525444542222222444445224444452544454222222233333333333333333333333333
    0000DA8514520253F24AAAAAAAD5D252008514520253F24111111100000000000000000160000000


     --- Record Number ---  5   ---  Record Length ---- 80

    0140  ..HEADER RECORD*******DSCRPTR HEADER RECORD!!!!!!!000000000000000000000000
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    33332200444445254445422222224545555244444525444542222222333333333333333333333333
    014000DA8514520253F24AAAAAAA433204208514520253F241111111000000000000000000000000

    *         _                            _                _
      ___    (_)_ __ ___  _ __   ___  _ __| |_  __  ___ __ | |_
     / _ \   | | '_ ` _ \| '_ \ / _ \| '__| __| \ \/ / '_ \| __|
    |  __/_  | | | | | | | |_) | (_) | |  | |_   >  <| |_) | |_
     \___(_) |_|_| |_| |_| .__/ \___/|_|   \__| /_/\_\ .__/ \__|
                         |_|                         |_|
    ;

     Process to import three SAS datasets

          1. Use lookup functions to get the names on SAS datasets inside the v5 transport
          2. Call read.xport to create a Rlist of dataframes corresponding to the sas datasets
          3. Delete the output datasets if they exist
          4. Write the three tables using the free evaluation copy of DULLES Research S-JDBC
          5. The d=code produces dataframeas class, prdsale and prdsal2 and corresponding sas datasets
          6. use the macro utl_optlen to optimize lengths (note the 255 byte fields)


    * could not get one of the apply functions or a loop to work with dbWriteTable;

    * see https://cran.r-project.org/web/packages/SASxport/SASxport.pdf
    * this creates 3 sas datasets from the single v5 xport file;

    %utl_submit_r64(%tslit(
    library(RJDBC);
    library(haven);
    library(SASxport);
    xpt="c:/xpt/sasxpt.xpt";
    lookup.xport(xpt);
    mta<-lookup.xport(xpt);
    mtanam<-names(mta);
    rlst<-read.xport(xpt,keep=mtanam);
    for (i in 1:length(mtanam) ) {
         outfyl<-paste("d:/sd1/",tolower(mtanam[i]),".sas7bdat",sep="");
         if (file.exists(outfyl)) { file.remove(outfyl) };
        };
    class<-rlst[[1]];
    prdsale<-rlst[[2]];
    prdsal2<-rlst[[3]];
    drv<- JDBC("com.dullesopen.jdbc.Driver","c:/carolina/carolina-jdbc-2.4.3.jar");
    conn <- dbConnect(drv, "jdbc:carolina:bulk:libnames=(dir='c:/xpt')", "", "");
    rc<-dbWriteTable(conn,"class",class);
    rc<-dbWriteTable(conn,"prdsale",prdsale);
    rc<-dbWriteTable(conn,"prdsal2",prdsal2);
    ));


    SAS xport file (output of lookup function)
    -------------------------------------------
    Filename: `\\E6420\d\xpt\sasxpt.xpt'

    Variables in data set `CLASS':
     dataset   name      type format flength fdigits iformat iflength ifdigits
       CLASS   NAME character              0       0                0        0
       CLASS    SEX character              0       0                0        0
       CLASS    AGE   numeric              0       0                0        0
       CLASS HEIGHT   numeric              0       0                0        0
       CLASS WEIGHT   numeric              0       0                0        0

    Variables in data set `PRDSALE':
     dataset     name      type  format flength fdigits iformat iflength ifdigits
     PRDSALE   ACTUAL   numeric  DOLLAR      12       2                0        0
     PRDSALE  PREDICT   numeric  DOLLAR      12       2                0        0
     PRDSALE  COUNTRY character   $CHAR      10       0                0        0
     PRDSALE   REGION character   $CHAR      10       0                0        0
     PRDSALE DIVISION character   $CHAR      10       0                0        0
     PRDSALE PRODTYPE character   $CHAR      10       0                0        0
     PRDSALE  PRODUCT character   $CHAR      10       0                0        0
     PRDSALE  QUARTER   numeric               8       0                0        0
     PRDSALE     YEAR   numeric               4       0                0        0
     PRDSALE    MONTH   numeric MONNAME       3       0                0        0

    Variables in data set `PRDSAL2':
     dataset     name      type  format flength fdigits iformat iflength ifdigits
     PRDSAL2  COUNTRY character   $CHAR      10       0                0        0
     PRDSAL2    STATE character   $CHAR      22       0                0        0
     PRDSAL2   COUNTY character   $CHAR      20       0                0        0
     PRDSAL2   ACTUAL   numeric  DOLLAR      12       2                0        0
     PRDSAL2  PREDICT   numeric  DOLLAR      12       2                0        0
     PRDSAL2 PRODTYPE character   $CHAR      10       0                0        0
     PRDSAL2  PRODUCT character   $CHAR      10       0                0        0
     PRDSAL2     YEAR   numeric               4       0                0        0
     PRDSAL2  QUARTER   numeric               8       0                0        0
     PRDSAL2    MONTH   numeric MONNAME       3       0                0        0
     PRDSAL2    MONYR   numeric   MONYY       0       0   MONYY        0        0

    *     _               _
      ___| |__   ___  ___| | __
     / __| '_ \ / _ \/ __| |/ /
    | (__| | | |  __/ (__|   <
     \___|_| |_|\___|\___|_|\_\

    ;

    * RUN OPTLEN macro;

    libname sd1 "c:/xpt";

    Contents of sd1.class

     Variables in Creation Order

    #    Variable    Type    Len

    1    NAME        Char    255
    2    SEX         Char    255
    3    AGE         Num       8
    4    HEIGHT      Num       8
    5    WEIGHT      Num       8

    * to fix the long charcater variables run this macro;

    %utl_optlen(inp=sd1.class,out=sd1.class);

    Contents of sd1.class

     Variables in Creation Order

    #    Variable    Type    Len

    1    NAME        Char      7
    2    SEX         Char      1
    3    AGE         Num       3
    4    HEIGHT      Num       8
    5    WEIGHT      Num       3

    * __                    _                            _
     / _|   ___  __ _ ___  (_)_ __ ___  _ __   ___  _ __| |_
    | |_   / __|/ _` / __| | | '_ ` _ \| '_ \ / _ \| '__| __|
    |  _|  \__ \ (_| \__ \ | | | | | | | |_) | (_) | |  | |_
    |_|(_) |___/\__,_|___/ |_|_| |_| |_| .__/ \___/|_|   \__|
                                       |_|
    ;

    libname xpt xport "d:/xpt/sasxpt.xpt";
    proc copy in=xpt out=work;
    run;quit;
    proc contents lib=work mt=data;
    run;quit;

               Member  Obs, Entries
      Name     Type     or Indexes   Vars  Label

      CLASS    DATA         19         5
      PRDSAL2  DATA         30        11   Furniture sales data
      PRDSALE  DATA         30        10   Furniture sales data

    *____                            _
    |___ \      ___ _ __   ___  _ __| |_
      __) |    / __| '_ \ / _ \| '__| __|
     / __/ _  | (__| |_) | (_) | |  | |_
    |_____(_)  \___| .__/ \___/|_|   \__|
    *              |_|           _
         __ _    _ __ ___   __ _| | _____    ___ _ __   ___  _ __| |_
        / _` |  | '_ ` _ \ / _` | |/ / _ \  / __| '_ \ / _ \| '__| __|
       | (_| |_ | | | | | | (_| |   <  __/ | (__| |_) | (_) | |  | |_
        \__,_(_)|_| |_| |_|\__,_|_|\_\___|  \___| .__/ \___/|_|   \__|
                                                |_|
    ;

    * you get an error but the file was created correctly;

    Options obs=30;

    %utlfkil(c:/xpt/sascpt.cpo);

    filename cpt "c:/xpt/sascpt.cpo";
    proc cport lib=sashelp file=cpt;
      select class prdsale prdsal2;
    run;quit;


    /*
     _            _                     _                 _
    | |__      __| | _____      ___ __ | | ___   __ _  __| |
    | `_ \    / _` |/ _ \ \ /\ / / `_ \| |/ _ \ / _` |/ _` |
    | |_) |  | (_| | (_) \ V  V /| | | | | (_) | (_| | (_| |
    |_.__(_)  \__,_|\___/ \_/\_/ |_| |_|_|\___/ \__,_|\__,_|

    */

    * If you cannot create the sas transport files using the sas
      code above you can use the R code below to download and decode code;
    * Note GitHub does not support binary downloads
      so I have created a base64 encoded version on the binary cport transport file;
    * The encoded version at https://tinyurl.com/d7h3vrbp can be decoded into
      the binary v5 transport file sascpt.cpo;
    * binary download of the v5 transport file;

    %utl_submit_r64('
    library("base64");
    url<-"https://tinyurl.com/d7h3vrbp";
    cpo="c:/xpt/sascpt.cpo";
    if (file.exists(cpo)) { file.remove(cpo) };
    tmp<-tempfile();
    download.file(url, tmp);
    base64::decode(tmp, cpo);
    ');

    /*                              _     _                    _
      ___      ___ _ __   ___  _ __| |_  | |__   ___  __ _  __| | ___ _ __
     / __|    / __| `_ \ / _ \| `__| __| | `_ \ / _ \/ _` |/ _` |/ _ \ `__|
    | (__ _  | (__| |_) | (_) | |  | |_  | | | |  __/ (_| | (_| |  __/ |
     \___(_)  \___| .__/ \___/|_|   \__| |_| |_|\___|\__,_|\__,_|\___|_|
                  |_|
    */
    %utlhex(uinflt =c:/xpt/sascpt.cpo,uotflt =c:/xpt/sascpt.txt);

    c:/xpt/sascpt.cpo
    c:/xpt/sascpt.txt

    Contents CPORT Header

    CONTETS OF HEADER;

        1. By default cport transport uses compression
           First 80 indicate compession

            --- Record Number ---  1   ---  Record Length ---- 80
           **COMPRESSED** **COMPRESSED** **COMPRESSED** **COMPRESSED** **COMPRESSED********

        2. Second 80 bytes provide control information for the first object

            --- Record Number ---  2   ---  Record Length ---- 80
           LIB CONTROL W32_10PR.. SAS9.4..I..D901SASHELP CLASS..VC5..RC19..RL40..1..S 0  0

        3. Also note the text

           --- Record Number ---  3   ---  Record Length ---- 80
           12 NO..0..1..ansi......Student Data..SPAN CONTROL -7..40..1..40..L..40...&..SPA

           I bevieve SPAN CONTROL records contain meta data for each variable
           V5 transport do not have SPAN CONTROL RECORDS


     --- Record Number ---  1   ---  Record Length ---- 80

    **COMPRESSED** **COMPRESSED** **COMPRESSED** **COMPRESSED** **COMPRESSED********
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    22444554554422222444554554422222444554554422222444554554422222444554554422222222
    AA3FD0253354AA0AA3FD0253354AA0AA3FD0253354AA0AA3FD0253354AA0AA3FD0253354AAAAAAAA


     --- Record Number ---  2   ---  Record Length ---- 80

    LIB CONTROL W32_10PR.. SAS9.4..I..D901SASHELP CLASS..VC5..RC19..RL40..1..S 0  0
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    44424445544253353355B02545323B84BA43335454445244455B9543B85433B85433B83B85232232
    C9203FE42FC0732F1002C603139E4C39C5490131385C003C133C9635C22319C82C40C81C73000000


     --- Record Number ---  3   ---  Record Length ---- 80

     12 NO..0..1..ansi......Student Data..SPAN CONTROL -7..40..1..40..L..40...&..SPA
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    233244B83B83B96676B8BCBC577666724676BC554424445544223B833B83B833B84B833B8B2BA554
    0120EFC40C91C51E39CACECE34545E404141C2301E03FE42FC0D7C240C51C640CACC840CEC6C6301


     --- Record Number ---  4   ---  Record Length ---- 80

    N CONTROL -1..118..2..38..L..118..NAME. ...................NAME............SPAN
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    424445544223B8333B83B833B84B8333B84444B20CB01B00B0000000B004444B0000B10B0BA55442
    E03FE42FC0D1C2118C42C638CACC7118CEE1D5C010C18C18C1804002CC2E1D5C1108C72C8C8301E0


     --- Record Number ---  5   ---  Record Length ---- 80

    CONTROL -1..117..2..37..L..117..NAME. .... ..............SEX............SPAN CON
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    4445544223B8333B83B833B84B8333B84444B20CB02B00B0000000B00545B0000B10B0BA55442444
    3FE42FC0D1C2117C42C637CACC7117CEE1D5C010C10C11C1103002CC2358C1201C72C8C9301E03FE


     --- Record Number ---  6   ---  Record Length ---- 80

    TROL -1..117..2..37..L..117..NAME. ................AGE...........SPAN CONTROL -1
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    5544223B8333B83B833B84B8333B84444B20CB00B0000000B00444B000B10B0BA554424445544223
    42FC0D1C2117C42C637CACC7117CEE1D5C010C58C1803001CC2175C130C81C8C9301E03FE42FC0D1

    /*                              _                                     _
      ___      ___ _ __   ___  _ __| |_    ___ ___  _ __ _ __ _   _ _ __ | |_
     / __|    / __| `_ \ / _ \| `__| __|  / __/ _ \| `__| `__| | | | `_ \| __|
    | (__ _  | (__| |_) | (_) | |  | |_  | (_| (_) | |  | |  | |_| | |_) | |_
     \___(_)  \___| .__/ \___/|_|   \__|  \___\___/|_|  |_|   \__,_| .__/ \__|
                  |_|                                              |_|
    */

    %utl_submit_r64('
    library("base64");
    url<-"https://tinyurl.com/4khm4pjm";
    cpo="c:/xpt/sascptcorrupt.cpo";
    if (file.exists(cpo)) { file.remove(cpo) };
    tmp<-tempfile();
    download.file(url, tmp);
    base64::decode(tmp, cpo);
    ');


    %utlhex(uinflt =c:/xpt/sascptcorrupt.cpo,uotflt =c:/xpt/sascptcorrupt.txt);

    Althouh there are several type of corruption the existance of many '0D0A'x
    is a tip-off.

      1. Notice the '0D0A'x on record 2

         --- Record Number ---  2   ---  Record Length ---- 80
        ..
        1.
        00
        DA

       * how many '0D0A'x are there;
       data _null_;
             infile "C:/xpt/sascptcorrupt.cpo" lrecl=32756 recfm=f;
             input;
             numcrlf=countw(_infile_,'0A0D'x);
             put numcrlf;
             stop;
       run;quit;

       There are 25 cr/lf in this small file.
       This is tipoff that orginator tried to upload records instead of
       binary stream.

    --- Record Number ---  1   ---  Record Length ---- 80

    **COMPRESSED** **COMPRESSED** **COMPRESSED** **COMPRESSED** **COMPRESSED********
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    22444554554422222444554554422222444554554422222444554554422222444554554422222222
    AA3FD0253354AA0AA3FD0253354AA0AA3FD0253354AA0AA3FD0253354AA0AA3FD0253354AAAAAAAA


     --- Record Number ---  2   ---  Record Length ---- 80

    ........0.................COUNTRYCountry...........$CHAR....SPAN CONTROL -1..138
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    00B00CB03B000B000000008B0044545554676777B00000B100024445B0B9554424445544223B8333
    DACE10C10C1DAC1DA070721CB23F5E4293F5E429C110DAC720543812C6C9301E03FE42FC0D1C2138


     --- Record Number ---  3   ---  Record Length ---- 80

    ..2..58..L..138..NAME.........:.......................d................PRODTYPEP
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    B83B833B84B8333B84444B01B00CB03B01B01000000B0000B00CB06B000B00000000B00554455545
    C42C658CACC7138CEE1D5CF6CE10C1AC16C16050E22CB2DACE10C14C1DAC1DA080C2CC202F449050


     --- Record Number ---  4   ---  Record Length ---- 80

    roduct Type...........$CHAR..R....SPAN CONTROL -1..133..2..53..L..133..NAME.....
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    76676725776B00000B100024445005B0B9554424445544223B8333B83B833B84B8333B84444B000B
    2F453404905C160DAC720543812002C3C3301E03FE42FC0D1C2133C42C653CACC7133CEE1D5CFDAC


     --- Record Number ---  5   ---  Record Length ---- 80

    .....n................PRODUCTProduct...........$CHAR..CHAR....SPAN CONTROL -1..1
    1...5....10...15...20...25...30...35...40...45...50...55...60...65...70...75...8
    00CB06B000B00000000B0055445455766767B00000B10002444500444500B9554424445544223B83
    E10C1EC1DAC1DA07072CC202F453402F4534C170DAC72054381200381200C9301E03FE42FC0D1C21
