# AngularLazyLoading
<b>Step by Step Guide  To Develop a Lazy Loading or On Scroll Loading of Data Using AngularJS</b><hr>

<b>Abstract :</b>
Hi friends now everyone is interested to implement the Lazy Loading concept while loading huge amount of data. This is an alternate to the paging concept, instead  of forcing an user to each time click some button to load the data it will loads the data automatically whenever the user scrolls down and it automatically will fetch the data in background and updates it in Fly.

<b>KeyWords :</b>
JavaScript, Jquery, AngularJS, Angular Directives, Lazy Loading .

<b>Pre Requirements :</b>
Basic knowledge about: JavaScript, Jquery, AngularJS .

<b>Introduction :</b>
A well implementation of Lazy Loading can be observed in Facebook, here what the developers have did instead of loading all the data at a time which may slows down the application, they have implemented the Lazy Loading concept. As a result of which the previous data gets loaded corresponding to the users scroll the more an user scrolls down more data gets loaded to the screen. So that the application performance does not gets hamper and also unnecessary data loading is reduced .
Here in my demo I will show you how to implement this cool concept very easily in few steps using basic knowledge of AngularJS, JavaScript ,jQuery . Here I will create a demo which will retrieve the data from a local JSON file each time user scrolls down and will update the users screen accordingly. 
Softwares or Libraries Required to Download :
Download all the library files <br>
a.	Angular JS<br>
b.	jQuery

<b>Implementation :</b> <br>

Here I have read the data from a local json file but you can very well use this code to read data from the DataBase and load it dynamically.
  <br><b>Step 1 : Create AngularApp</b><br>
  <pre>var lazyApp =  angular.module("lazyApp", []);</pre><br>
  
  <b>Step 2 : Create Angular Directive To Listen The Scroll Event</b><br>
  
  <pre>
    lazyApp.directive('lazyLoading', function($rootScope, $window){
    return function(scope, element, attr){
    	console.log("lazyLoading Directive is called ........");
        var lastScrollTop = 0;
        var container = angular.element($window);
        var scrollDownHandler = function(e){
            var scrollTop = $(window).scrollTop();
            var docHeight = $(document).height();
            var winHeight = $(window).height();
            var scrollPercent = (scrollTop) / (docHeight - winHeight);
            var scrollPercentRounded = Math.round(scrollPercent*100);
            var pageYoffset =  window.pageYOffset;
            var direction = ""
            if (scrollTop > lastScrollTop){
               direction = "down";
            } else {
              direction = "up";
            }
            lastScrollTop = scrollTop;

            if((scrollPercentRounded >= 30 && scrollPercentRounded <= 40)&& direction == "down"){
                scope.$apply(attr.lazyLoading)
            }
            
        }// End of scrollDownHandler() 

        container.bind("scroll", scrollDownHandler);
    }

})// End of  lazyLoading() directive
  </pre>

<b>Step 3 : Create Angular Controller and Data Loading Functions To Attach with the HTML Page</b><br>
<i>Note :</i> Here I have used JSON file to read the data, if you are intrested to fetch data from the Database then use or access the "lazyLoadControllerDB.js" file present inside the "js" folder. 
<pre>

lazyApp.controller("lazyLoadingController", function($scope,$q,$http){
	$scope.completeData = [];
	$scope.subData = [];

	// Function developed for fetdhing Data from the Json File (data.json) Present inside the "data" folder.
	$scope.fetchData = function(){
		var defered = $q.defer() ;
        $http({
            method : 'POST',
            url : "./data/data.json",
        })
        .success(function(data, status, header, config) {
            //resolve the promise
            defered.resolve(data);
            
        })
        .error(function(data, status, header, config) {
            //reject the promise
            defered.reject('ERROR');
            console.log("Error Occured ......");
        });
        //return the promise

   		return defered.promise;

	}// End of $scope.fetchData()

	// Function to be called for loading and fetching dynamically data 
	$scope.loadData = function(){

		$scope.subData = $scope.subData.concat($scope.completeData.splice($scope.subData.length,10));

	}// End of $scope.loadData()

	var getAjaxStatus = $scope.fetchData();
    getAjaxStatus.then(function(resolve){
        //console.log("Ajax Returned Succesfully ....", resolve);
        $scope.completeData = resolve;
        $scope.subData = $scope.completeData.splice(0,100);
    },function(reject){
        console.log("Ajax Failure Occured ....", reject);
    });

})// End of lazyLoadingController()
</pre><br>

<b>Step 4 : Using the Directive in HTML code</b><br>
I hope you must load all the required library files such as "angular.min.js" and "jQuery.min.js" in your HTML file.
add the referrence to the "lazyLoadingController.js", and include "ng-app"(ng-app = "lazyApp"), "ng-controller"(ng-controller="lazyLoadingController").
Below i have given a code which demostrates how you can use the ng-dircetive along with the function name.<br>
<b>Note : </b><i>Also you can refer the "index.html" file.</i><br>
<pre>
Ex : <div  ng-controller="lazyLoadingController" lazy-Loading="loadData()">
</pre>


<b>*** All Required AngularJS/JavaScript Codes in One ***</b><br>
<pre>
var lazyApp =  angular.module("lazyApp", []);

/*
    @Directive   : lazy-Loading
    @Date        : 06/05/2016
    @Author      : BiswaRanjan Samal
    @Email       : bichhubiswa@gmail.com

    @Description : This directive is developed for implementing the lazy-loading or On-Scroll loading of the data.
                   When the user scrolls down about 30% of the window the data gets loaded by calling the function which is given in the html.

    @Example     : <div  ng-controller="lazyLoadingController" lazy-Loading="loadData()">

    @Explantion  : Above is an Example which shows how to use this directive . "loadData()" ,is the function which will be called
                   when the user scrolls down. The data fetching function should be wriiten in this function,
                   the function name is user defiened , one can use its own function name but the function name should be replaced with "loadData()" .

*/

lazyApp.directive('lazyLoading', function($rootScope, $window){
    return function(scope, element, attr){
    	console.log("lazyLoading Directive is called ........");
        var lastScrollTop = 0;
        var container = angular.element($window);
        var scrollDownHandler = function(e){
            var scrollTop = $(window).scrollTop();
            var docHeight = $(document).height();
            var winHeight = $(window).height();
            var scrollPercent = (scrollTop) / (docHeight - winHeight);
            var scrollPercentRounded = Math.round(scrollPercent*100);
            var pageYoffset =  window.pageYOffset;
            var direction = ""
            if (scrollTop > lastScrollTop){
               direction = "down";
            } else {
              direction = "up";
            }
            lastScrollTop = scrollTop;

            if((scrollPercentRounded >= 30 && scrollPercentRounded <= 40)&& direction == "down"){
                scope.$apply(attr.lazyLoading)
            }
            
        }// End of scrollDownHandler() 

        container.bind("scroll", scrollDownHandler);
    }

})// End of  whenScrolledDown() directive




lazyApp.controller("lazyLoadingController", function($scope,$q,$http){
	$scope.completeData = [];
	$scope.subData = [];

	// Function developed for fetdhing Data from the Json File (data.json) Present inside the "data" folder.
	$scope.fetchData = function(){
		var defered = $q.defer() ;
        $http({
            method : 'POST',
            url : "./data/data.json",
        })
        .success(function(data, status, header, config) {
            //resolve the promise
            defered.resolve(data);
            
        })
        .error(function(data, status, header, config) {
            //reject the promise
            defered.reject('ERROR');
            console.log("Error Occured ......");
        });
        //return the promise

   		return defered.promise;

	}// End of $scope.fetchData()

	// Function to be called for loading and fetching dynamically data 
	$scope.loadData = function(){

		$scope.subData = $scope.subData.concat($scope.completeData.splice($scope.subData.length,10));

	}// End of $scope.loadData()

	var getAjaxStatus = $scope.fetchData();
    getAjaxStatus.then(function(resolve){
        //console.log("Ajax Returned Succesfully ....", resolve);
        $scope.completeData = resolve;
        $scope.subData = $scope.completeData.splice(0,100);
    },function(reject){
        console.log("Ajax Failure Occured ....", reject);
    });

})// End of lazyLoadingController()
</pre>



