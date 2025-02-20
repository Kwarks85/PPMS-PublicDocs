.. highlight:: js
.. default-domain:: js

.. _data-collection-javascript-tracking-client-api:

API
===

The following API allows the user to:

    * track page views
    * track visits on multiple domains and subdomains
    * track e-commerce events (successful orders, cart changes, product and
      category views)
    * track content impressions
    * manage custom variables to use them later
    * track clicked links to external domains and download files

.. contents:: Table of Contents

.. _jtc-api-command-queue:

Command queue
-------------

Code snippet with tracking code sets up globally accessible command queue
``_paq``. Users can issue commands by pushing them onto the command queue with
``_paq.push`` function. This is the recommended method of calling tracking
functions.

.. function:: _paq.push(command)

    Issues a command, e.g. track page view, custom event, site search etc.

    :param Array<string> command: Array containing a tracking function's `name`
        followed by its arguments. The number of arguments and their meaning
        are determined by the tracking function.

Example of usage (tracking a custom event by pushing a command to the command queue)::

    _paq.push(["trackEvent", "video", "video-paused", "intro.mp4", 15.2]);

Commands pushed onto the command queue will be executed once the JavaScript
Tracking Client loads. After that, ``_paq.push`` becomes synchronous, meaning
each command is executed at the moment of push.

.. _jtc-api-jstc-object:

JavaScript Tracking Client object
---------------------------------

JavaScript Tracking Client object offers an alternative method of calling tracking functions.
While it's more difficult to access than the :ref:`command queue<jtc-api-command-queue>`,
it allows to read the return value of a tracking function and makes
multi-tracker setups possible.

JavaScript Tracking Client object can be accessed using :ref:`Piwik.getTracker<jtc-api-Piwik.getTracker>`
or :ref:`Piwik.getAsyncTracker<jtc-api-Piwik.getAsyncTracker>` function.

.. _jtc-api-Piwik.getTracker:

.. function:: Piwik.getTracker(trackerUrl, siteId)

    Getter for JavaScript Tracking Client object.

    :param string trackerUrl: **Required** URL for JavaScript Tracking Client
    :param string siteId: **Required** Site ID that will be linked to tracked data.
    :returns: JavaScript Tracking Client object

    Example of usage (accessing JavaScript Tracking Client object and tracking a custom event)::

        var jstc = Piwik.getTracker("https://example.com/", "45e07cbf-c8b3-42f3-a6d6-a5a176f623ef");
        jstc.trackEvent("video", "video-paused", "intro.mp4", 15.2);

.. _jtc-api-Piwik.getAsyncTracker:

To access internal JavaScript Tracking Client object used for asynchronous tracking you must use
the ``Piwik.getAsyncTracker``.

.. function:: Piwik.getAsyncTracker(trackerUrl, siteId)

    Getter for JavaScript Tracking Client instance.

    :param string trackerUrl: **Required** URL for JavaScript Tracking Client
    :param string siteId: **Required** Site Id that will be linked to tracked data.
    :returns: JavaScript Tracking Client instance

    Example of usage (accessing JavaScript Tracking Client object and tracking a custom event)::

        var jstc = Piwik.getAsyncTracker("https://example.com/", "45e07cbf-c8b3-42f3-a6d6-a5a176f623ef");
        jstc.trackEvent("video", "video-paused", "intro.mp4", 15.2);

    JavaScript Tracking Client object is also accessible through ``this`` keyword in a special
    command pushed to command queue, where the first element of the command
    array is a custom function.::

        _paq.push([function () {
            // *this* is a JavaScript Tracking Client object
            this.addEcommerceItem("01725334", "USB-C chord")
            console.log(this.getEcommerceItems());
        }]);

    .. warning::

        JavaScript Tracking Client object can't be accessed before JavaScript Tracking Client file
        loads (ususally a `ppms.js` file).

.. _jtc-api-tracking-functions:

Tracking functions
------------------

Tracking functions collect and send data to CPP. They can by
called on a :ref:`JavaScript Tracking Client object<jtc-api-jstc-object>` or pushed to
the :ref:`command queue<jtc-api-command-queue>` as commands.





.. _jtc-api-page-views:

Page views
^^^^^^^^^^

.. _jtc-api-trackPageView:

.. function:: trackPageView([customPageTitle])

    Tracks page view of the page that the function was run on.

    :param string customPageTitle: **Optional** Custom page title, used only for this event

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackPageView"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackPageView();

    .. note::

        To overwrite page title for **all events** that will happen on the page
        (until a reload), use :ref:`setDocumentTitle<jtc-api-setDocumentTitle>`
        function.

    .. note::

        ``trackPageView`` is included in the default JavaScript Tracking Client setup snippet.
        It's likely you're already using it.





.. _jtc-api-custom-events:

Custom events
^^^^^^^^^^^^^

.. _jtc-api-trackEvent:

.. function:: trackEvent(category, action[, name[, value[, dimensions]]])

    Tracks custom event, e.g. when visitor interacts with the page.

    :param string category: **Required** Event category
    :param string action: **Required** Event action
    :param string name: **Optional** Event name
    :param number value: **Optional** Event value
    :param object dimensions: **Optional** :ref:`Custom dimensions<jtc-api-custom-dimensions-object>` to pass along with the custom event

    Example of usage (tracking when the visitor clicks on the cancel button with
    exit intent):

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackEvent", "Exit intent", "Click on button", "Cancel"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackEvent("Exit intent", "Click on button", "Cancel");

.. _jtc-api-goal-conversions:

Goal conversions
^^^^^^^^^^^^^^^^

.. _jtc-api-trackGoal:

.. function:: trackGoal(goalID[, conversionValue[, dimensions]])

    Tracks manual goal conversion.

    :param number|string goalID: **Required** Goal ID (integer or UUID)
    :param number conversionValue: **Optional** Conversion value (revenue)
    :param object dimensions: **Optional** :ref:`Custom dimensions<jtc-api-custom-dimensions-object>` to pass along with the conversion

    Example of usage (tracking conversion of goal *1* with value *15*):

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackGoal" 1, 15]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackGoal(1, 15);

.. _jtc-api-site-search:

Site search
^^^^^^^^^^^

.. _jtc-api-trackSiteSearch:

.. function:: trackSiteSearch(keyword[, category[, resultCount[, dimensions]]])

    Tracks search requests on a website.

    :param string keyword: **Required** What keyword the visitor entered into the search box
    :param string category: **Optional** Category selected in the search engine, can be set ``undefined`` if not applicable
    :param number searchCount: **Optional** The number of search results shown
    :param object dimensions: **Optional** :ref:`Custom dimensions<jtc-api-custom-dimensions-object>` to pass along with the site search event

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackSiteSearch", "stove", undefined, 20]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackSiteSearch("stove", undefined, 20);

.. _jtc-api-ecommerce:

E-commerce
^^^^^^^^^^

.. _jtc-api-addEcommerceItem:

.. function:: addEcommerceItem(productSKU[, productName[, productCategory[, productPrice[, productQuantity]]]])

    Adds a product to a virtual shopping cart. If a product with the same SKU
    is in the cart, it will be removed first. Does not send any data to the
    CPP.

    :param string productSKU: **Required** Product stock-keeping unit
    :param string productName: **Optional** Product name
    :param string|Array<string> productCategory: **Optional** Product category or an array of up to 5 categories
    :param number productPrice: **Optional** Product price
    :param number productQuantity: **Optional** The number of units

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["addEcommerceItem", "craft-311", "Unicorn Iron on Patch", "Crafts & Sewing", 499, 3]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.addEcommerceItem("craft-311", "Unicorn Iron on Patch", "Crafts & Sewing", 499, 3);

    .. note::

        This function does not send any data to CPP. It only
        prepares the virtual shopping cart to be sent with
        :ref:`trackEcommerceCartUpdate<jtc-api-trackEcommerceCartUpdate>`
        or :ref:`trackEcommerceOrder<jtc-api-trackEcommerceOrder>`.

    .. warning::

        The state of the virtual shopping cart is not persisted in browser
        storage. You must add all products again after a page reload.

    .. warning::

        Adding a product with a SKU that has been previously added will first
        remove the old product, e.g.:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["addEcommerceItem", "72625151", "Yellow notebook 150 pages", "School supplies", 10.00, 1]); // 1 item with sku 72625151
                _paq.push(["addEcommerceItem", "72625151", "Yellow notebook 150 pages", "School supplies", 10.00, 2]); // 2 items with sku 72625151, not 3!

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.addEcommerceItem("72625151", "Yellow notebook 150 pages", "School supplies", 10.00, 1); // 1 item with sku 72625151
                jstc.addEcommerceItem("72625151", "Yellow notebook 150 pages", "School supplies", 10.00, 2); // 2 items with sku 72625151, not 3!

.. _jtc-api-removeEcommerceItem:

.. function:: removeEcommerceItem(productSKU)

    Removes a product with the provided SKU from a virtual shopping cart. If
    multiple units of that product are in the virtual cart, all of them will be
    removed. Does not send any data to the CPP.

    :param string productSKU: **Required** stock-keeping unit of a product to remove

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["removeEcommerceItem", "craft-311"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.removeEcommerceItem("craft-311");

    .. note::

        This function does not send any data to CPP. It only
        prepares the virtual shopping cart to be sent with
        :ref:`trackEcommerceCartUpdate<jtc-api-trackEcommerceCartUpdate>`
        or :ref:`trackEcommerceOrder<jtc-api-trackEcommerceOrder>`.

    .. warning::

        The state of the virtual shopping cart is not persisted in browser
        storage. You must add all products again after a page reload.

.. _jtc-api-clearEcommerceCart:

.. function:: clearEcommerceCart()

    Removes all items from a virtual shopping cart. Does not send any data to
    the CPP.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["clearEcommerceCart"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.clearEcommerceCart();

    .. note::
        This function does not send any data to CPP. It only
        prepares the virtual shopping cart to be sent with
        :ref:`trackEcommerceCartUpdate<jtc-api-trackEcommerceCartUpdate>`
        or :ref:`trackEcommerceOrder<jtc-api-trackEcommerceOrder>`.

    .. warning::

        The state of the virtual shopping cart is not persisted in browser
        storage. You must add all products again after a page reload.

.. _jtc-api-getEcommerceItems:

.. function:: getEcommerceItems()

    Returns a copy of items from a virtual shopping cart. Does not send any
    data to the CPP.

    :returns: Object containing all tracked items (format: ``Object<productSKU, Array[productSKU, productName, productCategory, price, quantity]>``)

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function () { console.log(this.getEcommerceItems()); }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                console.log(jstc.getEcommerceItems());

    Example return value::

        {
            "52441051": ["52441051", "SUPER Notebook 15\" Ocean Blue", "Laptops", 2200, 1],
            "19287236": ["19287236", "Earbuds COOL PRO x300 BT", "Accessories", 85, 2],
        }

    .. warning::

        The state of the virtual shopping cart is not persisted in browser
        storage. You must add all products again after a page reload.

.. _jtc-api-setEcommerceView:

.. function:: setEcommerceView([productSKU[, productName[, productCategory[, productPrice]]]])

    Tracks product or category view. Must be followed by a :ref:`page view<jtc-api-page-views>`.

    :param string productSKU: **Optional** Product stock-keeping unit.
    :param string productName: **Optional** Product name.
    :param string|Array<string> productCategory: **Optional** Category or an array of up to 5 categories.
    :param number productPrice: **Optional** Category or an array of up to 5 categories.

    When tracking **product views**, provide ``productSKU`` and optionally
    other parameters.

    When tracking **category views**, provide only ``productCategory``. Skip
    ``productSKU``, ``productName`` and ``productPrice`` parameters supplying
    ``undefined`` where necessary.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setEcommerceView", undefined, undefined, "Crafts & Sewing"]); // category view
                _paq.push(["trackPageView"]);

                _paq.push(["setEcommerceView", "craft-311", "Unicorn Iron on Patch", "Crafts & Sewing", 499]); // product view
                _paq.push(["trackPageView"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setEcommerceView(undefined, undefined, "Crafts & Sewing"); // category view
                jstc.trackPageView();

                jstc.setEcommerceView("craft-311", "Unicorn Iron on Patch", "Crafts & Sewing", 499); // product view
                jstc.trackPageView();

    .. warning::

        ``setEcommerceView`` does not send data itself. It must be followed by
        a call to :ref:`trackPageView<jtc-api-trackPageView>`.

.. _jtc-api-trackEcommerceCartUpdate:

.. function:: trackEcommerceCartUpdate(cartAmount)

    Tracks items present in a virtual shopping cart (registered with :ref:`addEcommerceItem<jtc-api-addEcommerceItem>`);

    :param number cartAmount: **Required** The total value of items in the cart

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackEcommerceCartUpdate", 250]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackEcommerceCartUpdate(250);

    .. todo::
        Why JavaScript Tracking Client doesn't count cartAmount by itself? Why user must do this?

    .. warning::

        Make sure all products from the cart have been registered using
        ``addEcommerceItem`` before tracking a cart update. Remember that when
        a page is reloaded, the cart resets and all products must be registered again.

.. _jtc-api-trackEcommerceOrder:

.. function:: trackEcommerceOrder(orderID, orderGrandTotal[, orderSubTotal[, orderTax[, orderShipping[, orderDiscount]]]])

    Tracks a successfully placed e-commerce order with items present in a
    virtual cart (registered using :ref:`addEcommerceItem<jtc-api-addEcommerceItem>`).

    :param string orderID: **Required** String uniquely identifying an order
    :param number orderGrandTotal: **Required** Order Revenue grand total - tax, shipping and discount included
    :param number orderSubTotal: **Optional** Order subtotal - without shipping
    :param number orderTax: **Optional** Order tax amount
    :param number orderShipping: **Optional** Order shipping cost
    :param number orderDiscount: **Optional** Order discount amount

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackEcommerceOrder", "3352", 499, 399, 0, 100]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackEcommerceOrder("3352", 499, 399, 0, 100);

.. warning::

    ``trackEcommerceOrder`` function clears the list with registered e-commerce items.





.. _jtc-api-custom-variables:

Custom Variables
^^^^^^^^^^^^^^^^

.. deprecated:: 5.5
    We strongly advise using custom dimensions instead.

.. _jtc-api-setCustomVariable:

.. function:: setCustomVariable(index, name[, value[, scope]])

    Sets a custom variable that can be used later.

    :param number index: **Required** Index from 1 to 5 where the variable is stored
    :param string name: **Required** Name of the variable
    :param string value: **Optional** Value of the variable, limited to 200 characters
    :param string scope: **Optional** Scope of the variable, ``"visit"`` or ``"page"``. The default value is ``"visit"``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setCustomVariable", 1, "AspectRatio", "16:9", "visit"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setCustomVariable(1, "AspectRatio", "16:9", "visit");

    .. note::

        A custom variable with the ``"visit"`` scope will be saved for an entire session, you don't need to set it on every page.

    .. warning::

        Index is separate for each variable scope.

.. _jtc-api-deleteCustomVariable:

.. function:: deleteCustomVariable(index[, scope])

    Removes a previously set custom variable.

    :param number index: **Required** Number from 1 to 5 where variable is stored
    :param string scope: **Optional** Scope of the variable, ``"visit"`` or ``"page"``. The default value is ``"visit"``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["deleteCustomVariable", 1, "visit"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.deleteCustomVariable(1, "visit");

.. _jtc-api-getCustomVariable:

.. function:: getCustomVariable(index[, scope])

    Returns the value of a previously set custom variable.

    :param number index: **Required** Number from 1 to 5 where variable is stored
    :param string scope: **Optional** Scope of the variable, ``"visit"`` or ``"page"``. The default value is ``"visit"``.

    :rtype:  Array[string, string]|boolean
    :returns: Custom variable value as an array with name and value if the custom variable exists or ``false`` if it doesn't.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function() {
                    var customVariable = this.getCustomVariable(1, "visit");
                    console.log(customVariable);
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var customVariable = jstc.getCustomVariable(1, "visit");
                console.log(customVariable);

    Example return value::

        ["theme", "dark-01"]

.. _jtc-api-storeCustomVariablesInCookie:

.. function:: storeCustomVariablesInCookie()

    Enables storing ``"visit"`` type custom variables in a first party cookie.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["storeCustomVariablesInCookie"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.storeCustomVariablesInCookie();

.. _jtc-api-custom-dimensions:

Custom Dimensions
^^^^^^^^^^^^^^^^^

.. _jtc-api-setCustomDimensionValue:

.. function:: setCustomDimensionValue(customDimensionID, customDimensionValue)

    .. versionadded:: 15.3

    Sets a custom dimension to be used later.

    :param number customDimensionID: **Required** ID of a custom dimension
    :param string customDimensionValue: **Required** Value of a custom dimension

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setCustomDimensionValue", 3, "loginStatus"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setCustomDimensionValue(3, "loginStatus");

    .. warning::

        When you set a custom dimension, its value will be used in all tracking
        requests within a page load.

    .. warning::

        This function does not send any data to the CPP. It
        prepares a custom dimension to be sent with following events, e.g. page
        view, e-commerce events, outlink or download events.

.. _jtc-api-deleteCustomDimension:

.. function:: deleteCustomDimension(customDimensionID)

    Removes a custom dimension with the specified ID.

    :param number customDimensionID: **Required** ID of a custom dimension

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["deleteCustomDimension", 3]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.deleteCustomDimension(3);

.. _jtc-api-getCustomDimensionValue:

.. function:: getCustomDimensionValue(customDimensionID)

    .. versionadded:: 15.3

    Returns the value of a custom dimension with the specified ID.

    :param number customDimensionID: **Required** ID of a custom dimension
    :returns: Value set with :ref:`setCustomDimensionValue<jtc-api-setCustomDimensionValue>`
    :rtype: string

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function() {
                    var customDimension = this.getCustomDimensionValue(3);
                    console.log(customDimension);
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var customDimension = this.getCustomDimensionValue(3);

.. _jtc-api-setCustomDimension:

.. function:: setCustomDimension(customDimensionID, customDimensionValue)

    .. deprecated:: 15.3
        Function ``setCustomDimension`` is deprecated due to the difficulty of
        use (passed values should be URL encoded). Please use
        :ref:`setCustomDimensionValue<jtc-api-setCustomDimensionValue>`
        instead.

    Sets a custom dimension to be used later.

    :param number customDimensionID: **Required** ID of a custom dimension
    :param string customDimensionValue: **Required** Value of a custom dimension (should be URL encoded)

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setCustomDimension", 3, "loginStatus"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setCustomDimension(3, "loginStatus");

    .. warning::

        When you set a Custom Dimension, that value will be used in all
        tracking requests within a page load.

    .. warning::
        This function does not send any data to the CPP. It sets a
        Custom Dimension to be sent with following events, e.g. page view,
        e-commerce events, outlink or download events.

.. _jtc-api-getCustomDimension:

.. function:: getCustomDimension(customDimensionID)

    .. deprecated:: 15.3
        Function ``getCustomDimension`` is deprecated due to the difficulty of
        use (returned values are URL-encoded). Please use
        :ref:`getCustomDimensionValue<jtc-api-getCustomDimensionValue>`
        instead.

    Returns the value of a custom dimension.

    :param number customDimensionID: **Required** ID of a custom dimension
    :returns: Value set with :ref:`setCustomDimension<jtc-api-setCustomDimension>`
    :rtype: string

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([ function() {
                    var customDimension = this.getCustomDimension(3);
                    console.log(customDimension);
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var customDimension = jstc.getCustomDimension(3);
                console.log(customDimension);

.. _jtc-api-custom-dimensions-object:

Custom dimensions object
""""""""""""""""""""""""

Some tracking functions accept an optional ``dimensions`` parameter. You can
use it to pass additional custom dimensions along with the tracked event.
Custom dimension object might look like this::

    {
        "dimension1": "hello",
        "dimension4": "nice%20to%20see%20you",
        "dimension5": "goodbye"
    }

.. warning::

    Keys in a custom dimension object must be in ``"dimensionX"`` format, where
    ``X`` is the ID of a custom dimension. Keys that don't match this format
    will be ignored.

.. warning::

    Custom dimension values **must be percent-encoded**. To encode a string,
    pass it through ``encodeURIComponent`` function, e.g. ``encodeURIComponent("Äpfel?")``.





.. _jtc-api-content-tracking:

Content Tracking
^^^^^^^^^^^^^^^^

.. _jtc-api-impressions:

Impressions
"""""""""""

.. _jtc-api-trackAllContentImpressions:

.. function:: trackAllContentImpressions()

    Scans the entire DOM for content blocks and tracks impressions after all
    page elements load. It does not send duplicates on repeated calls unless
    ``trackPageView`` was called in between ``trackAllContentImpressions``
    invocations.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackAllContentImpressions"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackAllContentImpressions();

.. _jtc-api-trackVisibleContentImpressions:

.. function:: trackVisibleContentImpressions([checkOnScroll[, watchInterval]])

    Scans DOM for all visible content blocks and tracks impressions.

    :param boolean checkOnScroll: **Optional** Whether to scan for visible content on ``scroll`` event. Default value: ``true``.
    :param number watchInterval: **Optional** Delay, in milliseconds, between scans for new visible content. Periodic checks can be disabled by passing ``0``. Default value: ``750``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackVisibleContentImpressions", true, 2000]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackVisibleContentImpressions(true, 2000);

    .. warning::

        Neither option can be changed after the initial setup.

    .. warning::

        ``trackVisibleContentImpressions`` will not detect content blocks placed in a scrollable element.

.. _jtc-api-trackContentImpressionsWithinNode:

.. function:: trackContentImpressionsWithinNode(domNode)

    Scans ``domNode`` (with its children) for all content blocks and tracks
    impressions.

    :param Node domNode: **Required** DOM node with content blocks (elements with ``data-track-content`` attribute) inside

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                var element = document.querySelector("#impressionContainer");
                _paq.push(["trackContentImpressionsWithinNode", element]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var element = document.querySelector("#impressionContainer");
                jstc.trackContentImpressionsWithinNode(element);

    .. note::

        It can be used with ``trackVisibleContentImpressions`` to track only
        visible content impressions.

.. _jtc-api-trackContentImpression:

.. function:: trackContentImpression(contentName, contentPiece, contentTarget)

    Tracks manual content impression event.

    :param string contentName: **Required** Name of a content block
    :param string contentPiece: **Required** Name of the content that was displayed (e.g. link to an image)
    :param string contentTarget: **Required** Where the content leads to (e.g. URL of some external website)

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackContentImpression", "promo-video", "https://example.com/public/promo-01.mp4", "https://example.com/more"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackContentImpression("promo-video", "https://example.com/public/promo-01.mp4", "https://example.com/more");

.. _jtc-api-logAllContentBlocksOnPage:

.. function:: logAllContentBlocksOnPage()

    Print all content blocks to the console for debugging purposes.

    Example output::

        [
            {
                "name": "promo-video",
                "piece": "https://example.com/public/promo-01.mp4",
                "target": "https://example.com/more"
            }
        ]

.. _jtc-api-interactions:

Interactions
""""""""""""

.. _jtc-api-trackContentInteractionNode:

.. function:: trackContentInteractionNode(domNode[, contentInteraction])

    Tracks interaction with a block in domNode. Can be called from code placed
    in ``onclick`` attribute.

    :param Node domNode: **Required** Node marked as content block or containing content blocks. If content block can't be found, nothing will tracked.
    :param string contentInteraction: **Optional** Name of interaction (e.g. ``"click"``). Default value: ``"Unknown"``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                var domNode = document.querySelector("#add-image");
                _paq.push(["trackContentInteractionNode", domNode, "clicked"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var domNode = document.querySelector("#add-image");
                jstc.trackContentInteractionNode(domNode, "clicked");

    Example of usage in ``onclick`` attribute:

    .. code-block:: html

        <button onclick="function(){_paq.push(['trackContentInteractionNode', this, 'clicked']);}">Click me!</button>

.. _jtc-api-trackContentInteraction:

.. function:: trackContentInteraction(contentInteraction, contentName, contentPiece, contentTarget)

    Tracks manual content interaction event.

    :param string contentInteraction: **Required** Type of interaction (e.g. ``"click"``)
    :param string contentName: **Required** Name of a content block
    :param string contentPiece: **Required** Name of the content that was displayed (e.g. link to an image)
    :param string contentTarget: **Required** Where the content leads to (e.g. URL of some external website)

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackContentInteraction", "clicked", "trackingWhitepaper", "document", "http://cooltracker.tr/whitepaper"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackContentInteraction("clicked", "trackingWhitepaper", "document", "http://cooltracker.tr/whitepaper");

    .. warning::
        Use this function in conjunction with ``trackContentImpression``, as it
        can only be mapped with an impression by ``contentName``.





.. _jtc-api-download-and-outlink:

Download and Outlink
^^^^^^^^^^^^^^^^^^^^

.. _jtc-api-trackLink:

.. function:: trackLink(linkAddress, linkType[, dimensions[, callback]])

    Manually tracks outlink or download event with provided values.

    :param string linkAddress: **Required** URL address of the link
    :param string linkType: **Required** Type of the link, ``"link"`` for outlink, ``"download"`` for download
    :param object dimensions: **Optional** :ref:`Custom dimensions<jtc-api-custom-dimensions-object>` to pass along with the link event
    :param function callback: **Optional** Function that should be called after tracking the link

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackLink", "http://www.example.com/example", "link"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackLink("http://www.example.com/example", "link");

    Example of usage in ``onclick`` attribute:

    .. code-block:: html

        <button onclick="_paq.push(['trackLink', 'http://www.example.com/example', 'link'])">
            Click me!
        </button>

.. _jtc-api-enableLinkTracking:

.. function:: enableLinkTracking(enable)

    Enables or disables automatic link tracking. If enabled, left, right and
    middle clicks on links will be treated as opening a link. Opening a links
    to an external site (different domain) creates an outlink event. Opening a
    link to a downloadable file creates a download event.

    :param boolean enable: **Required** Whether to enable automatic link tracking. The default value is ``true``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackPageView"]);
                _paq.push(["enableLinkTracking"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackPageView();
                jstc.enableLinkTracking();

    .. note::

        ``enableLinkTracking`` is part of the default Tag Manager's tracking code snippet.
        It's likely your setup already has it.

    .. note::

        Outlinks events are tracked only when a link points to a different
        (external) domain. If that domain belongs to you and you don't want to
        track outlinks when visitors open it, use :ref:`setDomains<jtc-api-setDomains>`
        function to define internal domains and subdomains.

    .. warning::

        ``enableLinkTracking`` should be called right after the first
        ``trackPageView`` or ``trackEvent``.

.. _jtc-api-setIgnoreClasses:

.. function:: setIgnoreClasses(classes)

    Set a list of class names that indicate a link should not be tracked.

    :param string|Array<string> classes: **Required** CSS class name or an array of class names

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setIgnoreClasses", ["do-not-track", "ignore-link"]]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setIgnoreClasses(["do-not-track", "ignore-link"]);

    .. note::

        Elements with ``piwik-ignore`` and ``piwik_ignore`` classes are always
        ignored.

.. _jtc-api-setLinkClasses:

.. function:: setLinkClasses(classes)

    Sets a list of class names that indicate whether a link is an outlink and
    not download.

    :param string|Array<string> classes: **Required** CSS class name or an array of class names

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setLinkClasses", "this-is-an-outlink"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setLinkClasses("this-is-an-outlink");

    .. note::

        Elements with ``piwik-link`` or ``piwik_link`` class are always
        treated as outlinks.

.. _jtc-api-setDownloadClasses:

.. function:: setDownloadClasses(classes)

    Sets a list of class names that indicate whether a list is a download and
    not an outlink.

    :param string|Array<string> classes: **Required** CSS class name or an array of class names

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setDownloadClasses", "this-is-a-download"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setDownloadClasses("this-is-a-download");

    .. note::

        Elements with ``download`` attribute, ``piwik-download`` class or
        ``piwik_download`` class are always treated as downloads.

    .. note::

        Links containing a :ref:`known file extension<jtc-api-setDownloadExtensions>`
        will be treated as a downloads as well.

.. _jtc-api-setDownloadExtensions:

.. function:: setDownloadExtensions(extensions)

    Overwrites the list of file extensions indicating that a link is a download.

    :param string|Array<string> extensions: **Required** List of extensions to
        be set. Can be written as string, e.g. ``"zip|rar"``, or an array, e.g.
        ``["zip", "rar"]``.

    Links containing a known file extension are treated as downloads and not
    outlinks. We check for extensions at the end of URL path and in query
    parameter values. Below are examples of URL with extensions detected.

    * http\://example.com/path/file\ **.zip**
    * http\://example.com/path/file\ **.zip**\ #hello
    * http\://example.com/path/file\ **.zip**\ ?a=102
    * http\://example.com/path/?a=file\ **.zip**
    * http\://example.com/path/?a=file\ **.zip**\ &b=29

    The default download extensions list contains the following extensions:

    ``7z``, ``aac``, ``apk``, ``arc``, ``arj``, ``asf``, ``asx``, ``avi``, ``azw3``, ``bin``, ``csv``,
    ``deb``, ``dmg``, ``doc``, ``docx``, ``epub``, ``exe``, ``flv``, ``gif``, ``gz``, ``gzip``,
    ``hqx``, ``ibooks``, ``jar``, ``jpg``, ``jpeg``, ``js``, ``mobi``, ``mp2``, ``mp3``, ``mp4``,
    ``mpg``, ``mpeg``, ``mov``, ``movie``, ``msi``, ``msp``, ``odb``, ``odf``, ``odg``, ``ods``,
    ``odt``, ``ogg``, ``ogv``, ``pdf``, ``phps``, ``png``, ``ppt``, ``pptx``, ``qt``, ``qtm``, ``ra``,
    ``ram``, ``rar``, ``rpm``, ``sea``, ``sit``, ``tar``, ``tbz``, ``tbz2``, ``bz``, ``bz2``, ``tgz``,
    ``torrent``, ``txt``, ``wav``, ``wma``, ``wmv``, ``wpd``, ``xls``, ``xlsx``, ``xml``, ``z``, ``zip``

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setDownloadExtensions", "mhj|docx"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setDownloadExtensions("mhj|docx");

    .. warning::

        The list of download extensions is not persisted in the browser. It has
        to be configured on every page load.

.. _jtc-api-addDownloadExtensions:

.. function:: addDownloadExtensions(extensions)

    Adds new extensions to the download extensions list.

    :param string|Array<string> extensions: **Required** List of extensions to
        be added. Can be written as string, e.g. ``"7z|apk|mp4"``, or an array,
        e.g. ``["7z","apk","mp4"]``.

    .. warning::

        The list of download extensions is not persisted in the browser. It has
        to be configured on every page load.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["addDownloadExtensions", "mhj|docx"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.addDownloadExtensions("mhj|docx");

.. _jtc-api-removeDownloadExtensions:

.. function:: removeDownloadExtensions(extensions)

    Removes extensions from the download extensions list.

    :param string|Array<string> extensions: **Required** List of extensions to
        remove. Can be written as string, e.g. ``"zip|rar"``, or an array, e.g.
        ``["zip", "rar"]``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["removeDownloadExtensions", "mhj|docx"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.removeDownloadExtensions("mhj|docx");

    .. warning::

        The list of download extensions is not persisted in the browser. It has
        to be configured on every page load.





.. _jtc-api-user-management:

User management
^^^^^^^^^^^^^^^

.. _jtc-api-setUserId:

.. function:: setUserId(userID)

    Sets user ID, which will help identify a user of your application across
    many devices and browsers.

    :param string userID: **Required** Non-empty, unique ID of a user in application

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setUserId", "19283"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setUserId("19283");

    .. todo:: is user id persistent?

.. _jtc-api-resetUserId:

.. function:: resetUserId()

    Clears previously set ``userID``, e.g. when visitor logs out.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["resetUserId"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.resetUserId();

.. _jtc-api-setUserIsAnonymous:

.. function:: setUserIsAnonymous(isAnonymous)

    Enables or disables anonymous tracking (anonymous = without consent). Does
    not send any data to tracking CPP. The next emitted event will have
    anonymous mode set accordingly.

    :param boolean isAnonymous: **Required** Whether visitor is anonymous

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setUserIsAnonymous", true]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setUserIsAnonymous(true);

.. _jtc-api-deanonymizeUser:

.. function:: deanonymizeUser()

    Disables anonymous tracking and sends deanonymization event to the CPP. 
    Recommended method for disabling anonymous tracking.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["deanonymizeUser"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.deanonymizeUser();

.. _jtc-api-getVisitorId:

.. function:: getVisitorId()

    Returns 16-character hex ID of the visitor.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function () {
                    var visitorID = this.getVisitorId();
                    console.log(visitorID);
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var visitorID = jstc.getVisitorId();
                console.log(visitorID);

.. _jtc-api-getVisitorInfo:

.. function:: getVisitorInfo()

    Returns visitor information.

    :rtype: Array<string>
    :returns: String array with the following visitor info:

        0. new visitor flag indicating new (``"1"``) or returning (``"0"``) visitor
        1. visitor ID (16-character hex number)
        2. first visit timestamp (UNIX epoch time)
        3. previous visit count (``"0"`` for first visit)
        4. current visit timestamp (UNIX epoch time)
        5. last visit timestamp (UNIX epoch time or ``""`` if N/A)
        6. last e-commerce order timestamp (UNIX epoch time or ``""`` if N/A)

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function () {
                    var info = this.getVisitorInfo();
                    console.log(info);
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var info = jstc.getVisitorInfo();
                console.log(info);

    Example output::

        [
            "0",
            "6d85cb0b727eca52",
            "1624261490",
            "12",
            "1631115486",
            "1631115483",
            "1630590788"
        ]





.. _jtc-api-cookie-management:

Cookie management
^^^^^^^^^^^^^^^^^

.. _jtc-api-enableCookies:

.. function:: enableCookies()

    Enables all first party cookies. Cookies will be created on the next
    tracking request.

    .. note:: JavaScript Tracking Client has cookies enabled by default.

.. _jtc-api-disableCookies:

.. function:: disableCookies()

    Disables all first party cookies. Existing cookies will be deleted in the
    next page view.

.. _jtc-api-deleteCookies:

.. function:: deleteCookies()

    Deletes existing tracking cookies on the next page view.

.. _jtc-api-hasCookies:

.. function:: hasCookies()

    Returns ``true`` if cookies are enabled in this browser.

.. _jtc-api-setCookieNamePrefix:

.. function:: setCookieNamePrefix(prefix)

    Sets the prefix for analytics tracking cookies. Default is ``"_pk_"``.

    :param string prefix: **Required** String that will replace default analytics tracking cookies prefix.

.. _jtc-api-setCookieDomain:

.. function:: setCookieDomain(domain)

    Sets the domain for the analytics tracking cookies.

    :param string domain: **Required** Domain that will be set as cookie domain. For enabling subdomain you can use wildcard sign or dot.

.. _jtc-api-setCookiePath:

.. function:: setCookiePath(path)

    Sets the analytics tracking cookies path.

    :param string path: **Required** Path that will be set, default is ``"/"``.

.. _jtc-api-setSecureCookie:

.. function:: setSecureCookie(secure)

    Toggles the secure cookie flag on all first party cookies (if you are
    using HTTPS).

    :param boolean secure: **Required** Whether to add secure flag to cookies.

.. _jtc-api-setVisitorCookieTimeout:

.. function:: setVisitorCookieTimeout(seconds)

    Sets the expiration time of visitor cookies.

    :param number seconds: **Required** Number of seconds after which the cookie will expire. Default is 13 months.

.. _jtc-api-setReferralCookieTimeout:

.. function:: setReferralCookieTimeout(seconds)

    Sets the expiration time of referral cookies.

    :param number seconds: **Required** Number of seconds after which the cookie will expire. Default is 6 months.

.. _jtc-api-setSessionCookieTimeout:

.. function:: setSessionCookieTimeout(seconds)

    Sets the expiration time of session cookies.

    :param number seconds: **Required** Number of seconds after which the cookie will expire. Default is 30 minutes.

.. _jtc-api-setVisitorIdCookie:

.. function:: setVisitorIdCookie()

    Sets cookie containing :term:`analytics ID`.

    .. note::

        It's needed only when JavaScript Tracking Client instance is created without use of
        :func:`_paq.push` and script needs to know :term:`analytics ID` before
        first tracking request is sent. Make sure that it is called after all
        methods that configure cookie are called (e.g. :func:`setCookieNamePrefix`,
        :func:`setCookieDomain`, :func:`setCookiePath`, etc.).





.. _jtc-api-cross-domain-linking:

Cross domain linking
^^^^^^^^^^^^^^^^^^^^

.. _jtc-api-enableCrossDomainLinking:

.. function:: enableCrossDomainLinking()

    Enables cross domain linking. Visitors across domains configured with
    :ref:`setDomains<jtc-api-setDomains>` function will be linked by
    passing visitor ID parameter in links.

.. _jtc-api-disableCrossDomainLinking:

.. function:: disableCrossDomainLinking()

    Disables cross domain linking.

.. _jtc-api-isCrossDomainLinkingEnabled:

.. function:: isCrossDomainLinkingEnabled()

    Returns boolean telling whether cross domain linking is enabled.

.. _jtc-api-setCrossDomainLinkingTimeout:

.. function:: setCrossDomainLinkingTimeout(seconds)

    Changes the time in which two visits across domains will be linked. The
    default timeout is 180 seconds (3 minutes).

    :param number seconds: **Required** Number of seconds in which two visits across domains will be linked

.. _jtc-api-getCrossDomainLinkingUrlParameter:

.. function:: getCrossDomainLinkingUrlParameter()

    Returns the name of a cross domain URL parameter (query parameter by
    default) holding visitor ID. This is ``"pk_vid"`` by default.

    Example usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function () {
                    var parameter = this.getCrossDomainLinkingUrlParameter();
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var parameter = jstc.getCrossDomainLinkingUrlParameter();

    .. note::

        If your application creates links dynamically, you'll have to add this parameter manually, e.g.

        .. code-block:: js

            var url = "http://myotherdomain.com/path/?" + jstc.getCrossDomainLinkingUrlParameter();
            $element.append('<a href="' + url + '">link</a>');


.. _jtc-api-customCrossDomainLinkDecorator:

.. function:: customCrossDomainLinkDecorator(urlDecorator)

    Sets custom cross domains URL decorator for injecting visitor ID into URLs.
    Used when cross domain linking is enabled (see :js:func:`enableCrossDomainLinking`).

    :param function urlDecorator: **Required** Function injecting a parameter to a URL address

    .. function:: urlDecorator(url, value, name)

        Decorator function accepts link URL, parameter name, parameter value
        (visitor ID) and returns a URL containing the parameter data.

        :param string url: **Required** Link URL
        :param string value: **Required** Value of visitor ID that should be passed via URL
        :param string name: **Required** Name of visitor ID parameter used by JavaScript Tracking Client (can be customized)
        :return: Decorated URL or ``null`` (no change in URL)
        :rtype: string|null

    Example of usage (value sent via URL query parameter - equivalent of default
    implementation):

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["customCrossDomainLinkDecorator", function (url, value, name) {
                    var parsedUrl = new URL(url);
                    parsedUrl.searchParams.append(name, value);
                    return parsedUrl.href;
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.customCrossDomainLinkDecorator(function (url, value, name) {
                    var parsedUrl = new URL(url);
                    parsedUrl.searchParams.append(name, value);
                    return parsedUrl.href;
                }]);

    .. todo:: Is anyone actually overwriting the default decorator?

.. _jtc-api-customCrossDomainLinkVisitorIdGetter:

.. function:: customCrossDomainLinkVisitorIdGetter(urlParser)

    Sets custom cross domain URL parser for extracting visitor ID from URLs.
    Should extract data injected by URL decorator (set via
    :js:func:`customCrossDomainLinkDecorator`). The getter should return
    visitor ID extracted from page URL (used by :js:func:`enableCrossDomainLinking`).

    :param function urlParser: **Required** Function extracting a visitor ID from a URL address

    .. function:: urlParser(url, name)

        Parser function accepts page URL, parameter name and returns parameter
        value (visitor ID).

        :param string url: **Required** Page URL
        :param string name: **Required** Name of parameter holding visitor ID
        :return: Visitor ID value (parsed from URL)
        :rtype: string

    Example usage (value sent via URL query parameter - equivalent of default
    implementation):

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["customCrossDomainLinkVisitorIdGetter", function (url, name) {
                    return (new URL(url)).searchParams.get(name) || "";
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.customCrossDomainLinkVisitorIdGetter(function (url, name) {
                    return (new URL(url)).searchParams.get(name) || "";
                });

    .. todo:: Is anyone actually overwriting the default visitor ID getter?





.. _jtc-api-jstc-configuration:

JavaScript Tracking Client configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _jtc-api-setDomains:

.. function:: setDomains(domains)

    Allows to define a list of internal domains. Used in :ref:`outlink tracking<jtc-api-download-and-outlink>`
    for determining whether a link is an outlink and in :ref:`cross domain linking<jtc-api-cross-domain-linking>`
    for determining which links should have visitor ID parameter injected.

    :param Array<string> domains: **Required** A list of internal domains. Domains can contain wildcards: ``"*"``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setDomains", ["*.example.com", "*.example.co.uk"]]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setDomains(["*.example.com", "*.example.co.uk"]);

.. _jtc-api-setDocumentTitle:

.. function:: setDocumentTitle(title)

    Overwrites document title internally. All events sent afterwards will use
    the provided document title. The title shown in a browser window is not
    affected.

    :param string title: **Required** Custom title

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setDocumentTitle", document.title.toLocaleLowerCase()]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setDocumentTitle(document.title.toLocaleLowerCase());

.. _jtc-api-setTimingDataSamplingOnPageLoad:

.. function:: setTimingDataSamplingOnPageLoad(sampling)

    Configures page performance data collection. With non-zero sampling
    (5 by default), some page views will issue a page performance measurement.

    :param number sampling: **Required** Page performance sampling, integer between 0 and 100. 0 disables page performance data collection. 100 measures every page load.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setTimingDataSamplingOnPageLoad", 0]); // disables page performance data collection
                _paq.push(["setTimingDataSamplingOnPageLoad", 5]); // 5% of page views will by followed by a page performance measurement, this is the default behavior
                _paq.push(["setTimingDataSamplingOnPageLoad", 30]); // 30% of page views will be followed by a page performance measurement
                _paq.push(["setTimingDataSamplingOnPageLoad", 100]); // 100% of page views will be followed by a page performance measurement

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setTimingDataSamplingOnPageLoad(0); // disables page performance data collection
                jstc.setTimingDataSamplingOnPageLoad(5); // 5% of page views will by followed by a page performance measurement, this is the default behavior
                jstc.setTimingDataSamplingOnPageLoad(30); // 30% of page views will be followed by a page performance measurement
                jstc.setTimingDataSamplingOnPageLoad(100); // 100% of page views will be followed by a page performance measurement

    .. note::

        The default sampling value is 5, meaning 5% of page loads will be measured.

    .. warning::

        This setting will have an effect only if it's used before the ``trackPageView``.

    .. warning::

        If a page is closed before it fully loads (e.g. visitor closes the tab
        immediately after opening the page), page performance data will not be
        collected.

.. _jtc-api-getTimingDataSamplingOnPageLoad:

.. function:: getTimingDataSamplingOnPageLoad()

    Returns page performance sampling number.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function () {
                    console.log(this.getTimingDataSamplingOnPageLoad());
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                console.log(jstc.getTimingDataSamplingOnPageLoad());

    Example output::

        5

.. _jtc-api-enableHeartBeatTimer:

.. function:: enableHeartBeatTimer()

    When a visitor is not producing any events (e.g. because they are reading an
    article or watching a video), we don't know if they are still on the page.
    This might skew page statistics, e.g. *time on page* value. *Heartbeat timer*
    allows us to determine how much time visitors spend on a page by sending
    heartbeats to the CPP as long as the page is in focus.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["enableHeartBeatTimer"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.enableHeartBeatTimer();

    .. note::
        The first heartbeat will be sent 15 seconds after the page load. The
        time between heartbeats increases with the number of heartbeats sent
        and stops at 5 minutes. When a page looses focus, heartbeats will be
        paused until the focus is restored. The last heartbeat is sent 30
        minutes after the page view.

.. _jtc-api-setLinkTrackingTimer:

.. function:: setLinkTrackingTimer(milliseconds)

    When a visitor produces an events and closes the page immediately afterwards,
    e.g. when opening a link, the request might get cancelled. To avoid loosing
    the last event this way, JavaScript Tracking Client will lock the page for a fraction of a
    second (if wait time hasn't passed), giving the request time to reach the
    CPP.

    ``setLinkTrackingTimer`` allows to change the default lock/wait time of 500ms.

    :param number milliseconds: **Required** How many milliseconds a request needs to reach the CPP.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setLinkTrackingTimer", 100]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setLinkTrackingTimer(100);

    .. note::

        Requests sent using ``beacon`` method do not lock the page.

    .. note::

        Contrary to what the function name suggests, ``setLinkTrackingTimer``
        affects all other types of events. In recent versions of JavaScript
        Tracking Client, links are sent using ``beacon`` method if available.

.. _jtc-api-getLinkTrackingTimer:

.. function:: getLinkTrackingTimer()

    Returns lock/wait time after a request set by :ref:`setLinkTrackingTimer<jtc-api-setLinkTrackingTimer>`.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push([function () {
                    var time = this.getLinkTrackingTimer();
                    console.log(time);
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                var time = jstc.getLinkTrackingTimer();
                console.log(time);

.. _jtc-api-setSiteInspectorSetup:

.. function:: setSiteInspectorSetup(enable)

    `Site Inspector <https://chrome.google.com/webstore/detail/piwik-pro-site-inspector/njcnagohlmamfijimejlnelenhahnoce>`_
    is a Chrome browser extension that helps visualize analytics data (e.g.
    click heat map, scroll map) on tracked pages. Default configuration of
    JavaScript Tracking Client will add configuration for this extension (in a page HTML), but it
    is possible to disable this behavior if you don't need it.

    :param boolean enable: **Required** Whether to enable site inspector support.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setSiteInspectorSetup", false]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setSiteInspectorSetup(false);





.. _jtc-api-advanced-usage:

Miscellaneous
^^^^^^^^^^^^^

.. _jtc-api-ping:

.. function:: ping()

    Ping method sends requests that are not related to any visitor action, but
    can still update the session. the most common use for this method is
    updating session custom dimensions or custom variables.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["ping"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.ping();

.. _jtc-api-addListener:

.. function:: addListener(domElement)

    Adds automatic link tracking to an HTML element. Can be used to track links
    added to a document after page load.

    :param DOMElement domElement: **Required** Element that should be tracked like a link.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["addListener", document.querySelector("#dynamically-added-link")]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.addListener(document.querySelector("#dynamically-added-link"));

    .. todo:: Shouldn't this function be private? Is it of any use to developers? They can track link manually.

.. _jtc-api-setRequestMethod:

.. function:: setRequestMethod(method)

    Sets the request method. ``GET`` and ``POST`` are valid methods. ``GET`` is
    the default.

    :param string method: **Required** Method that will be used in requests. Either ``"GET"`` or ``"POST"``.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setRequestMethod", "POST"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setRequestMethod("POST");

    .. todo:: Mention same domain or CORS setup for "POST" method

.. _jtc-api-setRequestContentType:

.. function:: setRequestContentType(contentType)

    Sets ``Content-Type`` header of tracking requests. Used when tracking using
    ``"POST"`` method (set by :ref:`setRequestMethod<jtc-api-setRequestMethod>`).

    :param string contentType: **Required** Content-Type value to be set.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setRequestContentType", "text/plain"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setRequestContentType("text/plain");

.. _jtc-api-setCustomRequestProcessing:

.. function:: setCustomRequestProcessing(function)

    Allows to access and modify query string before sending a page view or ping
    request.

    :param function function: **Required** Function accepting a query string and returning another query string.

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["setCustomRequestProcessing", function (query) {
                    var modifiedQuery = query.replace("rec=1", "rec=0");
                    return modifiedQuery;
                }]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.setCustomRequestProcessing(function (query) {
                    var modifiedQuery = query.replace("rec=1", "rec=0");
                    return modifiedQuery;
                });

    .. todo::

        Consider removing/deprecating this method for two reasons:
        1. It only affects pings and page views
        2. It is hard to use - doing anything useful with it requires parsing query parameter string

.. _jtc-api-enableJSErrorTracking:

.. function:: enableJSErrorTracking(unique)

    Enables tracking of unhandled JavaScript errors.

    :param boolean unique: **Optional** When set to true, tracker will send only unique errors from a page (duplicated errors will be ignored). Default: ``true``.

    .. note::

        Browsers may limit information about error details if it occurs in
        script loaded from different origin (see `details <https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onerror#notes>`_).

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["enableJSErrorTracking"]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.enableJSErrorTracking();

.. function:: trackError(error)

    Attempts to send error tracking request using same format as native errors caught by :js:func:`enableJSErrorTracking`.
    Such error request will still follow rules set for tracker, so it will be sent only when JS error tracking is enabled (:js:func:`enableJSErrorTracking` function was called before this attempt). It will also respect rules for tracking only unique errors.

    :param Error error: **Required** Error object (e.g. caught with ``try...catch``)

    Example of usage:

    .. tabs::

        .. group-tab:: Command queue

            .. code-block:: javascript

                _paq.push(["trackError", new Error("Uncaught SyntaxError")]);

        .. group-tab:: JavaScript Tracking Client object

            .. code-block:: javascript

                jstc.trackError(new Error("Uncaught SyntaxError"));
