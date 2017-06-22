<?php
error_reporting(0);
function head()
{
    $heads = <<<EOS
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
<Document>
EOS;
return $heads;        
}

    
function footer()
{
    $foots = <<<foot
</Document>
</kml>
foot;
return $foots;
}

    
function Placemark($inc,$date,$lon,$lat,$desc)
{
    $place = <<<EOS
<Placemark>
<name>$desc</name>
<description> <![CDATA[<header> <h3>$date</h3> </header> <p>$inc</p>]]> </description>
<Point>
<coordinates>$lon,$lat,0</coordinates>
</Point>
</Placemark>
EOS;
return $place;
}    


function dateToInt($date)
{
    if (strpos($date," ") == 0)
    {
        $first_slash = strpos($date,"/");
        $month = substr($date,1,$first_slash-1);
        $month = (int)$month;
    }
    
    else
    {
        $first_slash = strpos($date,"/");
        $month = substr($date,0,$first_slash);
        $month = (int)$month;
    }
    
    $sec_slash = strpos($date,"/",$first_slash+1);
    $day = substr($date,$first_slash+1,($sec_slash-$first_slash)-1);
    $day = (int)$day;
    $space_loc = strpos($date," ",$sec_slash);
    $year = substr($date,$sec_slash+1,($space_loc-$sec_slash)-1);
    $year = (int)$year;
    $first_col = strpos($date,":");
    $hour = substr($date,$space_loc+1,($first_col-$space_loc)-1);
    $hour = (int)$hour;
    $sec_col = strpos($date,":",$first_col+1);
    $min = substr($date,$first_col+1,($sec_col-$first_col)-1);
    $min = (int)$min;
    $second = substr($date,$sec_col+1);
    $second = (int)$second;
    $Date_arr = array($year,$month,$day,$hour,$min,$second);
    return $Date_arr;
}

function dateQuery($Placemarks,$start_year,$start_month,$start_day,$start_hour,$end_year,$end_month,$end_day,$end_hour)
{
    $datesInRange = array();
    foreach ($Placemarks as $Placemark)
    {
        if (($Placemark[5][0] >= $start_year) && ($Placemark[5][0] <= $end_year))
        {
            if (($Placemark[5][1] >= $start_month) && ($Placemark[5][1] <= $end_month))
            {
                if (($Placemark[5][2] >= $start_day) && ($Placemark[5][2] <= $end_day))
                {
                    if (($Placemark[5][3] >= $start_hour) && ($Placemark[5][3] <= $end_hour))
                    {
                        array_push($datesInRange,$Placemark);
                    }
                }
            }
        }
    }
    
    return $datesInRange;
}
 
function topicQuery($Placemarks,$topics)
{
    $descs = array();
    $added = array();
    $topicsQueried = array();
    foreach ($Placemarks as $Placemark)
    {
        array_push($descs,$Placemark[4]);
    }
    foreach ($topics as $topic)
    {
        foreach ($descs as $key => $desc)
        {
            if (in_array($key,$added) == TRUE)
            {
                continue;
            }
            if (strpos($desc,$topic) !== FALSE)
            {
                array_push($topicsQueried,$Placemarks[$key]);
                array_push($added,$key);
            }
        }
    }

    return $topicsQueried;
}

function topicParse($topics)
{
    $start_pos = 0;
    $topic_arr = array();
    do
    {
        if (strpos($topics,",",$start_pos) == FALSE)
        {
            array_push($topic_arr,strtoupper(substr($topics,$start_pos)));
            break;
        }
        $comma_loc = strpos($topics,",",$start_pos);
        array_push($topic_arr,strtoupper(substr($topics,$start_pos,$comma_loc-$start_pos)));
        $start_pos = $comma_loc + 1;
        if (strpos($topics,",",$start_pos) == FALSE)
        {
            array_push($topic_arr,strtoupper(substr($topics,$start_pos)));
        }
    } while (strpos($topics,",",$start_pos) != FALSE);
    
    return $topic_arr;
}



// Main 
 

$csvdata = array();
$file = fopen("artstest.csv","r");

while(! feof($file))
  {
  array_push($csvdata,(fgetcsv($file)));
  }

fclose($file);


$Placemarks = array();
foreach ($csvdata as $line)
{
    $Placemark = array();
    array_push($Placemark,$line[0],$line[1],$line[5],$line[4],$line[6],dateToInt($line[1]));
    array_push($Placemarks,$Placemark);
    
}
$Placemarks = array_slice($Placemarks,1);

if (($_GET['topics'] === NULL) && ($_GET['startyear'] === NULL))
{
    $Placemarks = $Placemarks;
}
else if (($_GET['topics'] !== NULL) && ($_GET['startyear'] === NULL))
{
    $topics = $_GET['topics'];
    $topics = topicParse($topics);
    $Placemarks = topicQuery($Placemarks,$topics);
}
else if (($_GET['topics'] === NULL) && ($_GET['startyear'] !== NULL))
{
    $start_year = (int)$_GET['startyear'];
    if ($_GET['startmonth'] === NULL)
    {
        $start_month = 1;
    }
    else
    {
        $start_month = (int)$_GET['startmonth'];
    }
    if ($_GET['endmonth'] === NULL)
    {
        $end_month = 12;
    }
    else
    {
        $end_month = (int)$_GET['endmonth'];
    }
    if ($_GET['startday'] === NULL)
    {
        $start_day = 1;
    }
    else
    {
        $start_day = (int)$_GET['startday'];
    }
    if ($_GET['endday'] === NULL)
    {
        $end_day = 31;
    }
    else
    {
        $end_day = (int)$_GET['endday'];
    }
    if ($_GET['starthour'] === NULL)
    {
        $start_hour = 0;
    }
    else
    {
        $start_hour = (int)$_GET['starthour'];
    }
    if ($_GET['endhour'] === NULL)
    {
        $end_hour = 24;
    }
    else
    {
        $end_hour = (int)$_GET['endhour'];
    }
    if ($_GET['endyear'] === NULL)
    {
        $error_arr = array('ERROR','ERROR',0,0,'YOU NEED AN END YEAR');
        array_push($Placemarks,$error_arr);
    }
    else
    {
        $end_year = (int)$_GET['endyear'];
        $Placemarks = dateQuery($Placemarks,$start_year,$start_month,$start_day,$start_hour,$end_year,$end_month,$end_day,$end_hour);
    }
}
else if (($_GET['topics'] !== NULL) && ($_GET['startyear'] !== NULL))
{
    $topics = $_GET["topics"];
    $topics = topicParse($topics);
    $Placemarks = topicQuery($Placemarks,$topics);
    $start_year = (int)$_GET['startyear'];
    if ($_GET['startmonth'] === NULL)
    {
        $start_month = 1;
    }
    else
    {
        $start_month = (int)$_GET['startmonth'];
    }
    if ($_GET['endmonth'] === NULL)
    {
        $end_month = 12;
    }
    else
    {
        $end_month = (int)$_GET['endmonth'];
    }
    if ($_GET['startday'] === NULL)
    {
        $start_month = 1;
    }
    else
    {
        $start_day = (int)$_GET['startday'];
    }
    if ($_GET['endday'] === NULL)
    {
        $end_day = 31;
    }
    else
    {
        $end_day = (int)$_GET['endday'];
    }
    if ($_GET['starthour'] === NULL)
    {
        $start_month = 0;
    }
    else
    {
        $start_hour = (int)$_GET['starthour'];
    }
    if ($_GET['endhour'] === NULL)
    {
        $end_hour = 24;
    }
    else
    {
        $end_hour = (int)$_GET['endhour'];
    }
    if ($_GET['endyear'] === NULL)
    {
        $error_arr = array('ERROR','ERROR',0,0,'YOU NEED AN END YEAR');
        array_push($Placemarks,$error_arr);
    }
    else
    {
        $end_year = (int)$_GET['endyear'];
        $Placemarks = dateQuery($Placemarks,$start_year,$start_month,$start_day,$start_hour,$end_year,$end_month,$end_day,$end_hour);
    }
}

header('Content-Type: application/kml'); 
echo head();
foreach($Placemarks as $mark)
{
    if ($mark[2] == NULL)
    {
        continue;
    }
    echo Placemark($mark[0],$mark[1],$mark[2],$mark[3],$mark[4]);
}
echo footer();

?>
