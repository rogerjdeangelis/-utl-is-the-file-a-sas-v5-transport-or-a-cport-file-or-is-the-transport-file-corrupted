# utl-is-the-file-a-sas-v5-transport-cport-or-is-the-tranport-file-corrupted
Is the file a sas v5 transport, cport or is the transport file corrupted
Is the file a sas vr transport, cport or is the tranport file corrupted.

*                _             _
  ___ ___  _ __ | |_ ___ _ __ | |_ ___
 / __/ _ \| '_ \| __/ _ \ '_ \| __/ __|
| (_| (_) | | | | ||  __/ | | | |_\__ \
 \___\___/|_| |_|\__\___|_| |_|\__|___/

;
   Contents

       1. First we create four transport files each containing 3 sas datasets.

            a. V5 using proc copy
            b. proc cport
            c. proc cport corupted

       2. Next we will examine the header and
          point out the meta data


       3. Finally we will create the original SAS datasets using
          (max character lenghs in R and Python is 255 bytes and not all datetime formats are honored)

            a. SAS
            b. R (without sas)
            c. Python (without SAS)

*                _          _                                        _
 _ __ ___   __ _| | _____  | |_ _ __ __ _ _ __  ___ _ __   ___  _ __| |_ ___
| '_ ` _ \ / _` | |/ / _ \ | __| '__/ _` | '_ \/ __| '_ \ / _ \| '__| __/ __|
| | | | | | (_| |   <  __/ | |_| | | (_| | | | \__ \ |_) | (_) | |  | |_\__ \
|_| |_| |_|\__,_|_|\_\___|  \__|_|  \__,_|_| |_|___/ .__/ \___/|_|   \__|___/
  __ _     _ __  _ __ ___   ___    ___ ___  _ __  _   _
 / _` |   | '_ \| '__/ _ \ / __|  / __/ _ \| '_ \| | | |
| (_| |_  | |_) | | | (_) | (__  | (_| (_) | |_) | |_| |
 \__,_(_) | .__/|_|  \___/ \___|  \___\___/| .__/ \__, |
          |_|                              |_|    |___/
;

options obs=30;
LIBNAME sasxpt XPORT "d:/xpt/sasxpt.xpt";
proc copy in=sashelp out=sasxpt;
  select class prdsale prdsal2;
run;quit;

*_                              _
| |__      ___ _ __   ___  _ __| |_
| '_ \    / __| '_ \ / _ \| '__| __|
| |_) |  | (__| |_) | (_) | |  | |_
|_.__(_)  \___| .__/ \___/|_|   \__|
              |_|
;

* we will import

filename cpo "d:/xpt/sascport.cpo";
proc cport lib=sashelp file=cpo;
 select class prdsale prdsal2; ;  * without the select statement it will import the entire library:
run;quit;

*                      _                                     _
  ___ _ __   ___  _ __| |_    ___ ___  _ __ _ __ _   _ _ __ | |_
 / __| '_ \ / _ \| '__| __|  / __/ _ \| '__| '__| | | | '_ \| __|
| (__| |_) | (_) | |  | |_  | (_| (_) | |  | |  | |_| | |_) | |_
 \___| .__/ \___/|_|   \__|  \___\___/|_|  |_|   \__,_| .__/ \__|
     |_|                                              |_|
;

* Same is true for V5 transport file;

I uploaded the file but not
with binary option. We often see some crlf('0D0A'x) in the corrupted file.

You can dounload the file from GitHub using R

outfyl <- "d:/xpt/sascport.cpo";
download.file(url, outfyl)



filename cpo "d:/xpt/sascportcrup.cpo";
proc cport lib=sashelp file=cpo ;
 select class classfit iris ;  * without the select statement it will import the entire library:
run;quit;

* upload without binary option;

filenane inp "d:/xpt/sascport.cpo";
filenane out "d:/xpt/sascportcrup.cpo";
data _null_;
  infile inp lrecl=80 recfm=f;
  file out lrecl=80 ;
  input;
  put _infile_;
run;quit;

proc cimport lib=work file="d:/xpt/sascportcrup.cpo";
run;quit;



