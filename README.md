# Gregorian-to-ethiopian-calendar
Overview:  HTML Form: Users enter a Gregorian date, which is submitted via POST. PHP Script: Extracts: Year, month, and day from the input date. Converts: Calculates the Ethiopian date by comparing with Ethiopian New Year and adjusting for leap years. Displays: Ethiopian date in yyyy-mm-dd format.
This is designed to be simple so that users can easily integrate this function into their own software. 
Provided by:yetsedaw.y@gmail.com.
Code:-
<!DOCTYPE html>
<html>
<head>
    <title>Convert Gregorian Date to Ethiopian Date</title>
</head>
<body>
    <form method="POST" action="">
        <label for="gdate">Enter Gregorian Date:</label>
        <input type="date" id="gdate" name="gdate">
        <button type="submit">Convert</button>
    </form>
    <?php
    if($_SERVER['REQUEST_METHOD']=='POST'&&isset($_POST['gdate'])){
        $_gdate=$_POST['gdate'];
        $year=(int)substr($_gdate,0,4);
        $month=(int)substr($_gdate,5,2);
        $day=(int)substr($_gdate,8,2);
        try{
            list($ethiopian_year,$ethiopian_month,$ethiopian_day)=to_ethiopian_date($year,$month,$day);
            echo "Ethiopian Date የኢትዮጵያ ቀን፡$ethiopian_year-$ethiopian_month-$ethiopian_day";
        }catch (Exception $e){
            echo 'Error:'.$e->getMessage();
        }
    }
    function leap_year($year){
        return($year%4==0&&$year%100!=0)||($year%400==0);
    }
    function to_ethiopian_date($year,$month,$day){
        $new_year_day_gregorian=11;// New year day for the Gregorian calendar
        $new_year_month_gregorian=9;// New year month for the Gregorian calendar(September)
        $new_year_day_ethiopian=1;// New year day for the Ethiopian calendar
        $new_year_month_ethiopian=1;// New year month for the Ethiopian calendar(Meskerem)
        if($month<$new_year_month_gregorian||($month==$new_year_month_gregorian&&$day<$new_year_day_gregorian)){
            $ethiopian_year=$year-8;
        }else{
            $ethiopian_year=$year-7;
        }
        $_gdate=DateTime::createFromFormat('Y-m-d',"$year-$month-$day");
        $gregorian_new_year=DateTime::createFromFormat('Y-m-d',"$year-$new_year_month_gregorian-$new_year_day_gregorian");
        if($_gdate<$gregorian_new_year){
            $gregorian_new_year->modify('-1 year');
        }
        $interval=$_gdate->diff($gregorian_new_year);
        $days_difference=$interval->days;
        if($days_difference<0){
            $days_difference+=365+(leap_year($year)?1:0);
        }
        $ethiopian_month=intdiv($days_difference,30)+1;
        $ethiopian_day=($days_difference%30);
        return [$ethiopian_year,$ethiopian_month,$ethiopian_day];
    }
    ?>
</body>
</html>

