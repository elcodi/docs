Menu
====

Create, manage and modify menus and nodes in a very simple and intuitive way.

* [Bundle Documentation](http://elcodi.io/docs/bundles/menu/)
* [Github Repository](https://github.com/elcodi/Menu)

The main goal of this component is the decoupling between how a menu must be 
created by a project, and the way all leafs are generated, filtered and 
modified, in order to give this responsibility to the actor who really has to
do it.

## Structure

Let's see a simple view of how Menu is modeled

```
Menu
  |
  |- Node1
  |   |
  |   |- Node2
  |   |   |
  |   |   |- Node3
  |   |
  |   |- Node4
  |
  |- Node5
```  

## Creating a new Menu

You can create a new Menu with their MenuNodes just using their factories. Let's
see an example of how to build a structure.

``` php
use Elcodi\Component\Menu\Factory\MenuFactory;
use Elcodi\Component\Menu\Factory\NodeFactory;

$menuNodeFactory = new NodeFactory();
$menuFactory->setEntityNamespace('Elcodi\Component\Menu\Entity\Menu\Node');
$menuNode = $menuFactory
    ->create()
    ->setName('Dashboard')
    ->setCode('dashboard')
    ->setUrl('http://myurl.com')
    ->setActiveUrls([
        'http://myurl.com/admin'
    ])
    ->setEnabled(true);
    
$menuFactory = new MenuFactory();
$menuFactory->setEntityNamespace('Elcodi\Component\Menu\Entity\Menu\Menu');
$menu = $menuFactory
    ->create()
    ->setName('admin')
    ->setDescription('Admin menu')
    ->setEnabled(true)
    ->addSubnode($menuNode);
```

## Managing menus

Using the class `MenuManager` you will be able to manage your menus. This 
manager is not simple, so has the responsibility to manage all menus using local
cache, Doctrine cache and Database repository.

``` php
use Doctrine\Common\Cache\CacheProvider;
use Doctrine\Common\Persistence\ObjectManager;

use Elcodi\Component\Core\Encoder\Interfaces\EncoderInterface;
use Elcodi\Component\Menu\Repository\MenuRepository;

$menuRepository = ...; // MenuRepository
$menuObjectManager = ...; // ObjectManager
$menuCache = ...; // CacheProvider
$menuEncoder = ...; //EncoderInterface
$menuCacheKey = 'menus';

$menuManager = new MenuManager(
    $menuRepository,
    $menuObjectManager,
    $menuCacheKey
);
$menuManager
    ->setCache($menuCache)
    ->setEncoder($menuEncoder);
    
$adminMenu = $menuManager->loadMenuByCode('admin');
```

Each menu, once loaded from database, is detached in a cascade mode. It means
that you cannot modify a menu using any kind of event and save it into database.

Lets see how a menu can be changed on the fly.

> Since now, when we refer to a Changer element, we are referring to Filters,
> Modifiers or Builders

## Cache

Of course this component is built on top of a simple cache layer, so each time 
a new menu is loaded from database, there are two different stages: before the 
menu is cached `ElcodiMenuStages::BEFORE_CACHE` and after the menu is cached 
`ElcodiMenuStages::AFTER_CACHE`.

The difference is that when you apply a changer before the result is cached, 
those changes will be reused until the menu is removed from the cache and 
rebuilt again.

``` php
use Elcodi\Component\Menu\Services\MenuFilterer;
use Elcodi\Component\Menu\ElcodiMenuStages;

$menuFilterer = new MenuFilterer();
$menuFilterer
    ->addMenuFilter(
        new MenuDisabledFilter(),
        [],
        ElcodiMenuStages::BEFORE_CACHE,
        0
    );
```

So, if one specific filter, a builder or a modifier must be executed in every
request, then must be defined as the after-cache stage.

## Specific menus

When we add a changer element, we can specify a set of menu elements where we
want this changes to be applied to. You can as well apply them to all menus, so
this parameter should be an empty array.

``` php
use Elcodi\Component\Menu\Services\MenuFilterer;
use Elcodi\Component\Menu\ElcodiMenuStages;

$menuFilterer = new MenuFilterer();
$menuFilterer
    ->addMenuFilter(
        new MenuDisabledFilter(),
        ['admin', 'store'],
        ElcodiMenuStages::BEFORE_CACHE,
        0
    );
```

In that case, we are setting that our filter should only be applied to menus 
called `admin` and `store`.

## Priority

Once we have several changers, then we can prioritize them by using priority
parameter.

``` php
use Elcodi\Component\Menu\Services\MenuFilterer;
use Elcodi\Component\Menu\ElcodiMenuStages;

$menuFilterer = new MenuFilterer();
$menuFilterer
    ->addMenuFilter(
        new MenuDisabledFilter(),
        ['admin'],
        ElcodiMenuStages::BEFORE_CACHE,
        0
    );
$menuFilterer
    ->addMenuFilter(
        new AnotherMenuFilter(),
        ['admin'],
        ElcodiMenuStages::BEFORE_CACHE,
        10
    );
```

In that case, and because more priority means earlier execution, filter
`AnotherMenuFilter` will be executed before than filter `MenuDisabledFilter`.

## Filtering menus

You can filter existing menus. It means that, considering any node, one by one,
you can decide if this node (and all subnodes) must continue being inside the 
menu.

If a node is considered as invalid, then all subnodes will, automatically, 
treated as invalids.

Let's see an example of a MenuFilter that removed a node if this one is not 
enabled.

```
use Elcodi\Component\Menu\Filter\Interfaces\MenuFilterInterface;
use Elcodi\Component\Menu\Entity\Menu\Interfaces\NodeInterface;

/**
 * Class MenuDisabledFilter
 */
class MenuDisabledFilter implements MenuFilterInterface
{
    /**
     * Filter a node once this has to be rendered
     *
     * @param NodeInterface $menuNode Menu node
     *
     * @return boolean Node must be rendered
     */
    public function filter(NodeInterface $menuNode)
    {
        return $menuNode->isEnabled();
    }
}
```

Then you must add this changer once your manager is instanced in order to use it
in Menu building time. In this example we will add a filter that will check 
when a menu is disabled in database. Then this menu node should be removed from
the tree.

``` php
use Elcodi\Component\Menu\Services\MenuFilterer;

$menuFilterer = new MenuFilterer();
$menuFilterer
    ->addMenuFilter(
        new MenuDisabledFilter(),
        [],
        ElcodiMenuStages::BEFORE_CACHE,
        0
    );

$menuManager = new MenuManager(
    $menuRepository,
    $menuObjectManager,
    $menuCacheKey
);
$menuManager
    ->setCache($menuCache)
    ->setEncoder($menuEncoder)
    ->addMenuChanger($menuFilterer)
    
$adminMenu = $menuManager->loadMenuByCode('admin');
```

If we check the menu tree, then we could draw something like that

```
Menu
  |
  |- Node1 <enabled>
  |   |
  |   |- Node2 <disabled>
  |   |   |
  |   |   |- Node3 <enabled>
  |   |
  |   |- Node4 <enabled>
  |
  |- Node5 <disabled>
```

So after applying this filters, we will have this menu

```
Menu
  |
  |- Node1 <enabled>
      |
      |- Node4 <enabled>
```

## Building menus

Once the menu is requested from the repository and detached from the object
manager, you can add more nodes in order to configure it.

Let's see an example that adds a new Node inside the Node dashboard. For this,
both entities Menu and Node have a method called `findNodeByKey` that returns,
if exists, the Node with given code, looking for inside all subnodes, 
recursively.

```
use Elcodi\Component\Menu\Builder\Interfaces\MenuBuilderInterface;
use Elcodi\Component\Menu\Entity\Menu\Interfaces\NodeInterface;
use Elcodi\Component\Menu\Factory\NodeFactory;

/**
 * Class MenuBuilderUser
 */
class MenuBuilderUser implements MenuBuilderInterface
{
    /**
     * Build the menu
     *
     * @param MenuInterface $menu Menu
     */
    public function build(MenuInterface $menuNode)
    {
        $menuNodeFactory = new NodeFactory();
        $menuFactory->setEntityNamespace('Elcodi\Component\Menu\Entity\Menu\Node');
        $userNode = $menuNodeFactory
            ->create()
            ->setName('User')
            ->setCode('user')
            ->setUrl('http://myurl.com/admin/user')
            ->setEnabled(true);

        $dashboardNode = $menu->findSubnodeByName('Dashboard');
        
        if ($dashboardNode instanceof NodeInterface) {
            $dashboardNode->addSubnode($userNode);
        }
    }
}
```

Then you must add this changer once your manager is instanced in order to use it
in Menu building time.

``` php
use Elcodi\Component\Menu\Services\MenuBuilder;

$menuBuilder = new MenuBuilder();
$menuBuilder
    ->addMenuBuilder(
        new MenuBuilderUser(),
        [],
        ElcodiMenuStages::BEFORE_CACHE,
        0
    );

$menuManager = new MenuManager(
    $menuRepository,
    $menuObjectManager,
    $menuCacheKey
);
$menuManager
    ->setCache($menuCache)
    ->setEncoder($menuEncoder)
    ->addMenuChanger($menuBuilder)
    
$adminMenu = $menuManager->loadMenuByCode('admin');
```

## Modifying menus

Maybe you want to modify a single menu. For this you can use the Menu Modifiers,
a type of changer that is intended, only, for single Menu Node modifications.

Let's see an example that sets `active` as true if, given a Request object, 
current route matches any of specified as active routes.

```
use Symfony\Component\HttpFoundation\Request;

use Elcodi\Component\Menu\Entity\Menu\Interfaces\NodeInterface;
use Elcodi\Component\Menu\Modifier\Interfaces\MenuModifierInterface;

/**
 * Class MenuActiveModifier
 */
class MenuActiveModifier implements MenuModifierInterface
{
    /**
     * Modifier the menu node
     *
     * @param NodeInterface $menuNode Menu node
     */
    public function modify(NodeInterface $menuNode)
    {
        $request = ...; // Request

        $currentRoute = $request->get('_route');

        if (in_array($currentRoute, $menuNode->getActiveUrls())) {
            $menuNode->setActive(true);
        }
    }
}
```

Then you must add this changer once your manager is instanced in order to use it
in Menu building time.

``` php
use Elcodi\Component\Menu\Services\MenuModifier;

$menuModifier = new MenuModifier();
$menuModifier
    ->addMenuModifier(
        new MenuActiveModifier(),
        [],
        ElcodiMenuStages::BEFORE_CACHE,
        0
    );

$menuManager = new MenuManager(
    $menuRepository,
    $menuObjectManager,
    $menuCacheKey
);
$menuManager
    ->setCache($menuCache)
    ->setEncoder($menuEncoder)
    ->addMenuChanger($menuModifier)
    
$adminMenu = $menuManager->loadMenuByCode('admin');
```