# Yii2 
> Yii2 fake data generator and random lorempixel image downloader.
> Generate fake user for testing purpose

This is a Yii2 console app. That can be run through command
```
php yii faker
```
Source:
```
<?php

namespace console\controllers;

use yii\console\Controller;
use console\models\Users;
use console\models\LogNotifications;
use yii\db\Expression;
use yii\helpers\Url;
use yii;

class FakerController extends Controller
{   
    public function actionIndex()
    {
        $this->actionUsers();
    }


    /**
     * @todo User faker method
     */
    public function actionUsers()
    {
        $log = [];

        $units = 10;
        $password = Yii::$app->getSecurity()->generatePasswordHash('Access');

        for($i=1;$i<=$units;$i++)
        {
            $faker = \Faker\Factory::create('en_US');
            $user = new Users;

            /**
             * @todo Massive Assignment of data
             */
            $user->userEmail = $faker->safeEmail;
            $user->userPassword = $password;
            $user->userName = $faker->name;
            $user->userNumber = $faker->phoneNumber;
            $user->userDOB = $faker->dateTimeThisCentury()->format('Y-m-d H:i:s');
            $user->userPicture = $faker->file('/var/www/html/random_images/', '/var/www/html/projects/frameworks/yii/tafoos/frontend/web/uploads/profile_images/', false);
            $user->userAllergicDishes = $faker->sentence(5);
            $user->userWallet = $faker->numberBetween(100,999);
            $user->userDeviceToken = $faker->sha1;
            $user->userAdded = $faker->dateTimeThisYear()->format('Y-m-d H:i:s');
            $user->userRole = $faker->randomElement(['User', 'Student']);
            $user->userStatus = 'Active';


            if($user->save(false))
            {
                echo 'Success: '.$user->pkUserID.PHP_EOL;
                $log['Success'][] = array($i, ['userID' => $user->pkUserID]);
            }
            else
            {
                echo 'Error: '.$i.PHP_EOL;
                $log['Error'][] = array($i, $user->getErrors());
            }
        }
    }


    /**
     * @todo Download lorem pixel images
     */
    public function actionImageDownload()
    {
        $log = [];
        
        $units = 10;
        $url = 'http://lorempixel.com/400/400/';
        $dir = '/var/www/html/random_images/';

        for($i=1;$i<=$units;$i++)
        {

            $randomFileName = Yii::$app->getSecurity()->generateRandomString().'.jpg';
            $content = file_get_contents($url);
            $saveFile = file_put_contents($dir.$randomFileName, $content);


            if($saveFile)
            {
                echo $randomFileName.PHP_EOL;
                $log['Success'][] = array($i, ['fileName' => $randomFileName]);
            }
            else
            {
                echo 'Error: '.$randomFileName.PHP_EOL;
                $log['Error'][] = array($i, $randomFileName);
            }
        }
    }
}
```