# How to add a new Image Resize adapter

Elcodi is served with a simple but very useful built-in image server. One of
the most important actions this server is intended for is resizing images.

## Scenario

You need your application to be deployed to a server with PHP GD, but you want
to use another image resize engine, for example an external API.

Elcodi provides you a set of limited adapters for image resizing, so we should
implement this new adapter. Elcodi provides you a simple, fast and amazing way
to do it without many pain

## Solution

You can create your own image resize engine by adding a new adapter. By default,
Elcodi is built with an implementation using the PHP GD Extension (you will
probably find this extension already installed with your regular PHP
distribution). This adapter can be easily replaced by creating a new class
implementing `CurrencyExchangeRatesProviderAdapterInterface`.

``` php
<?php

namespace Elcodi\Component\Media\Adapter\Resizer\Interfaces;

use Elcodi\Component\Media\ElcodiMediaImageResizeTypes;

/**
 * Interface ResizerAdapterInterface
 */
interface ResizeAdapterInterface
{
    /**
     * Interface for resize implementations
     *
     * @param string  $imageData Image Data
     * @param integer $height    Height value
     * @param integer $width     Width value
     * @param integer $type      Type
     *
     * @return string Resized image data
     */
    public function resize(
        $imageData,
        $height,
        $width,
        $type = ElcodiMediaImageResizeTypes::FORCE_MEASURES
    );
}
```

Then, you need to define your class as a service.

``` yml
services:

    #
    # My image resizer adapter
    #
    my_image_resizer_adapter:
        class: My\Image\Resizer\Adapter
```

And finally you need to expose your adapter implementation as the used by the
framework when resizing an existing image. You can do that by configuring the
MediaBundle and setting your new service id.

``` yml
elcodi_media:
    images:
        resize:
            client: my_image_resizer_adapter
```

Now, everytime an image needs to be resized, you will see your new adapter in
action.

### Resize types

Elcodi resize adapter must complain 6 different resize types. Each type is
defined using the constants placed in `ElcodiMediaImageResizeTypes`.

``` php

/**
 * Class ElcodiMediaImageResizeTypes
 */
final class ElcodiMediaImageResizeTypes
{
    /**
     * @var integer
     *
     * Do not resize the image
     */
    const NO_RESIZE = 0;

    /**
     * @var integer
     *
     * Resize mode FORCE_MEASURES sets the image to the desired size.
     * DOES NOT preserve original aspect ratio.
     * Best for fixed size images like banners
     */
    const FORCE_MEASURES = 1;

    /**
     * @var integer
     *
     * Resize mode INSET sets the image to the desired size.
     * Preserves original aspect ratio so it might not fill the box.
     * Best for large thumbnails
     */
    const INSET = 2;

    /**
     * @var integer
     *
     * Resize mode INSET_FILL_WHITE sets the image to the desired size.
     * Preserves original aspect ratio and adds white color to the background in order to fill the box.
     * Best for small thumbnails
     */
    const INSET_FILL_WHITE = 3;

    /**
     * @var integer
     *
     * Outbound resizing
     */
    const OUTBOUNDS_FILL_WHITE = 4;

    /**
     * @var integer
     *
     * Resize mode 5 sets the image to the desired size.
     * Preserves original aspect ratio and crops image for only get this area.
     */
    const OUTBOUND_CROP = 5;
}
```

As you can see in the adapter interface, by default this resize must force
measures.

### Adapters

These are our adapters included in the Core of the application. If you have
implemented another adapter and you think that can be interesting to share it,
then you can create a pull request with your work. We will appreciate it.

#### GD Extension Adapter

* Namespace - Elcodi\Component\Media\Adapter\Resizer\GDResizeAdapter
* DI name - `elcodi.media_resize_adapter.gd`

This extension doesn't need any extra installation

#### Imagemagick Adapter

* Namespace - Elcodi\Component\Media\Adapter\Resizer\ImageMagickResizeAdapter
* DI name - `elcodi.media_resize_adapter.imagemagick`

For more info just visit their [installation page](http://php.net/manual/en/imagick.setup.php)

> On the installation step you will be asked to provide the Imagick installation
> path. Ensure to configure the parameter imagick_convert_bin_path right
