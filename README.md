Purpose
--------------

ViewUtils is a collection of category methods that extend UIView with all the handy little properties and functionality that you always wished were built-in to begin with. ViewUtils covers:

* Nib loading - makes it easy to load views from nibs without a UIViewController
* Layout - set and get position and width/height properties individually
* Searching - retrieve subviews based on class, tag or arbitrary predicates
* Responder - find the first responder within a given view

Supported OS & SDK Versions
-----------------------------

* Supported build target - iOS 7.0 / Mac OS 10.9 (Xcode 5.0, Apple LLVM compiler 5.0)
* Earliest supported deployment target - iOS 6.0 / Mac OS 10.7
* Earliest compatible deployment target - iOS 4.3 / Mac OS 10.6

NOTE: 'Supported' means that the library has been tested with this version. 'Compatible' means that the library should work on this iOS version (i.e. it doesn't rely on any unavailable SDK features) but is no longer being tested for compatibility and may require tweaking or bug fixes to run correctly.


ARC Compatibility
------------------

ViewUtils works with both ARC and non-ARC projects. There is no need to exclude ViewUtils files from the ARC validation process, or to convert ViewUtils using the ARC conversion tool.


Thread Safety
--------------

UIView is inherently single-threaded, so ViewUtils methods should only be used on the main thread.


Installation
--------------

To use the ViewUtils categories in an app, just drag the ViewUtils.h and .m files (demo files and assets are not needed) into your project and import the header file into any class where you wish to make use of the ViewUtils functionality, or include it in your prefix.pch file to make it available globally within your project.


Extension Methods
-------------------

ViewUtils extends UIView with the following methods:

    + (id)instanceWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)bundleOrNil owner:(id)owner;
    
This method loads a view from a nib file. The view to be loaded must be the first object in the file. This method is useful for loading views such as UITableViewCells, or iCarousel item views. The nib file is cached, so subequent loads will perform better.
    
    - (void)loadContentsWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)bundleOrNil;
    
This method is similar to `instanceWithNibName:`, but instead of returning the first view in the nib file, this method loads that view and then adds it as a subview to the view on which the method is called, resizing the loaded view to fit the bounds of the superview if neccesary. This is especially useful if you want to create a complex view composed of reusable subviews, each stored in their own nib file.
    
    - (UIView *)viewMatchingPredicate:(NSPredicate *)predicate;
    
Returns either the view itself of the first subview that matches the specified predicate. If no subview matches, the method returns nil.
    
    - (UIView *)viewWithTag:(NSInteger)tag ofClass:(Class)viewClass;
    
Returns either the view itself of the first subview that has the specified tag value, *and* is of the specified class. This is safer than the regular `viewWithTag:` method.
    
    - (UIView *)viewOfClass:(Class)viewClass;
    
Returns either the view itself of the first subview of the specified class.
    
    - (NSArray *)viewsMatchingPredicate:(NSPredicate *)predicate;
    - (NSArray *)viewsWithTag:(NSInteger)tag;
    - (NSArray *)viewsWithTag:(NSInteger)tag ofClass:(Class)viewClass;
    - (NSArray *)viewsOfClass:(Class)viewClass;
    
These methods work exactly like their single-view equivalents, but instead of returning the first view that matches, they return all views that match. The search is performed in a depth-first fashion.

    - (UIView *)firstSuperviewMatchingPredicate:(NSPredicate *)predicate;
    - (UIView *)firstSuperviewOfClass:(Class)viewClass;
    - (UIView *)firstSuperviewWithTag:(NSInteger)tag;
    - (UIView *)firstSuperviewWithTag:(NSInteger)tag ofClass:(Class)viewClass;
    
These methods work like the viewMatching/Of/Width versions, but work *up* the view hierarchy instead of down and will return the first parent view that matches the specified criteria.

    - (BOOL)viewOrAnySuperviewMatchesPredicate:(NSPredicate *)predicate;
    - (BOOL)viewOrAnySuperviewIsKindOfClass:(Class)viewClass;

This method returns YES if the view, or any superview in in the chain matches the criteria. This is useful for event handling, for example if you'd like to know if a given touch was either on or within a given control or control type, e.g. so you can ignore gestures performed on specific views.
    
    - (BOOL)isSuperviewOfView:(UIView *)view;
    - (BOOL)isSubviewOfView:(UIView *)view;
    
These methods allow you to determine if a view is a child of another view. These methods will search the entire superview chain, not just a single level, so if one view is the superview *of the superview* of the other one, it will still return YES.

    - (UIViewController *)firstViewController;

This method uses the responder chain to identify the first view controller in the controller chain that is responsible for the view. So for example, if this method is called on a button, it will return the view controller that hosts the view that contains that button.

    - (UIView *)firstResponder;
    
This method returns the first responder if it is a subview of the view on which the method is called, or nil if not. If you don't know which subview tree the first responder might be located in, calling this method on the main window or the root view of the current frontmost view controller should return the desired result.
    
    - (void)setLeft:(CGFloat)left right:(CGFloat)right;
    - (void)setWidth:(CGFloat)width right:(CGFloat)right;
    - (void)setTop:(CGFloat)top bottom:(CGFloat)bottom;
    - (void)setHeight:(CGFloat)height bottom:(CGFloat)bottom;

It's often fiddly to set both the left and right edges of a view, or set the width without affecting the right-hand edge because the properties are linked. These setter methods make it a little easier to do this by allowing you to set both properties with a single method call.

    - (void)crossfadeWithDuration:(NSTimeInterval)duration;
    - (void)crossfadeWithDuration:(NSTimeInterval)duration completion:(void (^)(void))completion;
    
These methods use Core Animation to perform a crossfade transition of the specified duration. A crossfade is often useful if you want to animate the change of some view property that does not already support animation. A typical example would be changing the text in a UILabel or the image in a UIImageView.


Extension Properties
--------------------

ViewUtils extends UIView with the following properties for setting the frame position and dimensions:

    @property (nonatomic, assign) CGPoint origin;
    
The position within the superview of the view origin (the top-left corner). This is equivalent to `view.frame.origin`.
    
    @property (nonatomic, assign) CGSize size;
    
The outer dimensions of the view. This is equivalent to `view.frame.size`.
    
    @property (nonatomic, assign) CGFloat top;
    @property (nonatomic, assign) CGFloat left;
    @property (nonatomic, assign) CGFloat bottom;
    @property (nonatomic, assign) CGFloat right;
    
The positions of the four corners of the view within the superview. These positions are all relative to the top-left corner of the superview, so (for example) the `view.bottom` property is equivalent to `view.top + view.height`.
    
    @property (nonatomic, assign) CGFloat width;
    @property (nonatomic, assign) CGFloat height;
    
The outer dimensions of the view. These are equivalent to `view.size.width` and  `view.size.height` respectively.
    
    @property (nonatomic, assign) CGFloat x;
    @property (nonatomic, assign) CGFloat y;
    
The position of the anchor point for the view (typically the center) within the superview. These are equivqlent to `view.center.x` and `view.center.y` respectively.

    @property (nonatomic, assign) CGSize boundsSize;
    @property (nonatomic, assign) CGFloat boundsWidth;
    @property (nonatomic, assign) CGFloat boundsHeight;
    
The dimensions of the view bounds (the inner size). The bounds size is useful when the view has been transformed and you can no longer rely on the frame size to get or set the content dimensions accurately.

    @property (nonatomic, readonly) CGRect contentBounds;
    
Quite often you wish to position a subview to exactly fill its superview, but in order to do that you must contruct a CGRect with the width and height of the superview's bounds and an origin of 0,0. Unfortunately there is no guarantee that the `superview.bounds` will have an origin of 0,0 because in a UIWindow that has been rotated, or a UIScrollView that has been scrolled or zoomed, the bounds origin will have been changed in order to correctly align the contents within the view frame. The contentBounds property returns a rect that matches the bounds size, but always has an origin of 0,0.
    
    @property (nonatomic, readonly) CGPoint contentCenter;
    
Quite often you wish to position a subview in the exact center of its superview, but calculating the center of a view is a tedious piece of code to write. The contentCenter property returns this position.
   