SlimCachingManager
==================

Works under https://github.com/codeguy/Slim >= 2.3.0

The **SlimCachingManager** helps you to simplify caching and the delivery of the cached data of the Slim Framework resources. It stores the caching data for each resource (etag, expiry, lastmodified etc.) in ResourceHandlers
which can be written on your own by implementing the *IResourceHandler*.

It's helpful for resources which doesn't have any physical changes to detect that the resource has been changed (e.g. database query results). It's possible to define resources (wildcard notation possible) which you'd like to have cached. SlimCachingManager will
automatically check if the resource should or is being cached at the moment. The rest is quite simple.

You want to use **SlimCachingManager**? Just follow the steps at the bottom of the page. All your headers (Etag, Lastmodified, expiry) will be set by **SlimCachingManager**.

If you have any questions or suggestions to improve **SlimCachingManager** feel free to contact me. If you want to contribute just add a new branch and send me a pull request.

Best regards
Magnus

Usage
--------
1. Create instance of Slim
2. Add resources to the ResourceMapper which should be cached (Wildcards allowed!)
3. Add a before.dispatch hook or middleware (in the example it's done by hook)
4. Create instance of Slim\Http\Caching\ResourceMapper\Etag() or Slim\Http\Caching\ResourceMapper\Lastmodified() (depends on needed cache method)
5. Inject your own ResourceHandler into ResourceMapper
6. Inject the Slim application into ResourceMapper
7. Call method setHeaders()

Example
--------
	<?php
	
	# 1. Create Slim instance
	$app = new \Slim\Slim();
	
	# 2a. Add resource '/fetch/my/resource/' to my cache list
	Slim\Http\Caching\ResourceMapper\Base::addResourceWildcard( '/fetch/my/resource/', 24 );

	# 2b. It's also possible to pass a Array with the resource wildcards
	Slim\Http\Caching\ResourceMapper\Base::addResourceWildcard(
		Array(
			// Moderator data and list
			'/fetch/moderator/data/' => 24,
			'/fetch/moderator/list/' => 24,
		)
	);	
	
	# 3. You could also use a middleware. For that simple example i used a before.dispatch hook.
	$app->hook( 'slim.before.dispatch', function () use ( $app, $db ) {

		# 4. Create instance of your wished ResourceMapper (ETag() or Lastmodified())
		$cachingManager = new Slim\Http\Caching\ResourceMapper\ETag();

		# 5. Inject your ResourceHandler, Slim instance and finally set the headers
		$cachingManager->setHandler( new Ezd\Caching\ResourceHandler\FileStore\Json( 'cache.json' ) )
			->setApplication( $app ) #6.
			->setHeaders(); #7.

	});

	?>
	
If you want to have your data stored as a serialized string, just instantiate class "Serialized" instead of "Json"

Have fun!