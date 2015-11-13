# Media

Manage your media entities.

* [Component Repository](https://github.com/elcodi/Media)
* [Bundle Repository](https://github.com/elcodi/MediaBundle)

Of course, what is an E-commerce without images management. We are not focused 
on that, but we have created a tiny and simple image manager, that will provide
you an easy way of image upload and resize.

## What offers

This component mainly contains routes as public resources. You will be able as
well to modify the way images are resized by using adapters, and a set of events
for image manipulation.

You will find as well the minimum implementation of Image and File entity needed
to make it work.

## Upload an image: Route

First step for managing images is the uploading process. At the end, it is only
a controller proposed by the framework with an specific route defined.

If we take a look at Bamboo we will discover that we really have this route 
defined in file `routing_admin.yml`.

``` yaml
admin_media:
    resource: "@AdminMediaBundle/Resources/config/routing.yml"
```

of course, this piece of code is just an inclusion of an external `yaml` file,
provided by the bundle `MediaBundle`. Such file contains this code.

``` yaml
#
# Routing
#

elcodi.route.image_upload:
    path: /images/upload
    methods: POST
    defaults:
        _controller: elcodi.controller.image_upload:uploadAction
        
elcodi.route.image_resize:
    path: /images/{id}/resize/{height}/{width}/{type}.{_format}
    methods: GET
    defaults:
        _controller: elcodi.controller.image_resize:resizeAction

elcodi.route.image_view:
    path: /images/{id}/render.{_format}
    methods: GET
    defaults:
        _controller: elcodi.controller.image_resize:resizeAction
        height: 0
        width: 0
        type: 0

```

Let's focus only in the first route. Take in account that this route is defined
only as `POST`.

By default, this controller will check a field called `file`, but you can change
this value by overwriting the bundle configuration value

``` yaml
elcodi_media:
    images:
        upload:
            field_name: file
```

This value will be available in your container as a parameter with name
`elcodi.image_upload_field_name`.

## Upload an image: Response

Once you have uploaded your image, you will receive a response depending on the
action result.

If the upload process has been success, you will receive this response

``` json
{  
   "status":"ok",
   "response":{  
      "id":19,
      "extension":"jpg",
      "routes":{  
         "view":"\/images\/{id}\/render.{_format}",
         "resize":"\/images\/{id}\/resize\/{height}\/{width}\/{type}.{_format}",
         "delete":"\/admin\/media\/image\/{id}\/delete"
      }
   }
}
```

Otherwise, you will have a fail response

``` json
{  
   "status":"ko",
   "response":{  
      "id":19,
      "extension":"jpg",
      "routes":{  
         "view":"\/images\/{id}\/render.{_format}",
         "resize":"\/images\/{id}\/resize\/{height}\/{width}\/{type}.{_format}",
         "delete":"\/admin\/media\/image\/{id}\/delete"
      }
   }
}
```

## View an image

Once your image, you can access to it using an already built in route.

## Resize an image