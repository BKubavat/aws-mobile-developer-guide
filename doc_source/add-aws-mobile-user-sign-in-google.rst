.. _add-aws-mobile-user-sign-in-google:

##########################################
Add Google User Sign-in to Your Mobile App
##########################################


.. meta::
   :description: Integrating user sign-in


.. _google-config-overview:

Google Sign-in
==============


The Google identity provider for AWS Mobile Hub :ref:`user-sign-in` enables your users to sign in to your
AWS-backed app using their Google credentials. The SDK provides the Google look and feel for the
sign-in experience.


.. _google-config:

Set up Your Backend
===================

**To configure Google user sign-in**


#. Complete the :ref:`add-aws-mobile-user-sign-in-backend-setup` steps before using the
   integration steps on this page.

#. Use AWS Mobile Hub to deploy your backend services.


   #. Sign in to the `AWS Mobile Hub console <https://console.aws.amazon.com/mobilehub>`_.

   #. Choose :guilabel:`Create a new project`, type a name for it, and then choose :guilabel:`Create
      project`.

      Or select an existing project.

   #. Choose the :guilabel:`User Sign-in` tile, then choose :guilabel:`Google`.

   #. Type in your Google Web App Client ID, and the Google Android or iOS Client ID (or both), and
      then choose :guilabel:`Enable Google Sign-In`. To retrieve or create your Google Client IDs,
      see :ref:`auth-google-setup`.

      .. image:: images/add-aws-mobile-sdk-google.png
         :scale: 100
         :alt: Image of the Download Configuration Files button in the |AMH| console.

      .. only:: pdf

         .. image:: images/add-aws-mobile-sdk-google.png
            :scale: 50

      .. only:: kindle

         .. image:: images/add-aws-mobile-sdk-google.png
            :scale: 75

   #. Choose whether signing in to your app is :guilabel:`Optional` or :guilabel:`Required.`

#. Download your |AMH| project configuration file.

      #. In the |AMH| console, choose your project, and then choose the :guilabel:`Integrate` icon on the left.

      #. Choose :guilabel:`Download Configuration File` to get the :file:`awsconfiguration.json` file that connects your app to your backend.

         .. image:: images/add-aws-mobile-sdk-download-configuration-file.png
            :scale: 100 %
            :alt: Image of the Mobile Hub console when choosing Download Configuration File.

         *Remember:*

         Each time you change the |AMH| project for your app, download and use an updated :file:`awsconfiguration.json` to reflect those changes in your app. If NoSQL Database or Cloud Logic are changed, also download and use updated files for those features.


.. _google-app:

Add the SDK to Your app
=======================


Make sure to complete the :ref:`add-aws-mobile-user-sign-in-backend-setup`, steps before
using the integration steps on this page.

**To add a Google identity provider to your app**

.. container:: option

   Android - Java
      #. Add the backend service configuration file to your app.


         #. Right-click your app's :file:`res` folder, and then choose :guilabel:`New > Android
            Resource Directory`. Select :guilabel:`raw` in the :guilabel:`Resource type` dropdown
            menu.

            .. image:: images/add-aws-mobile-sdk-android-studio-res-raw.png
               :scale: 100
               :alt: Image of selecting a Raw Android Resource Directory in Android Studio.

            .. only:: pdf

               .. image:: images/add-aws-mobile-sdk-android-studio-res-raw.png
                  :scale: 50

            .. only:: kindle

               .. image:: images/add-aws-mobile-sdk-android-studio-res-raw.png
                  :scale: 75

         #. From the location where configuration files were downloaded in a previous step, drag
            :file:`awsconfiguration.json` into the :file:`res/raw` folder.

      #. Set up AWS Mobile SDK components with the following :ref:`add-aws-mobile-sdk-basic-setup` steps.


         #. :file:`AndroidManifest.xml` must contain:

            .. code-block:: xml
               :emphasize-lines: 0

                <uses-permission android:name="android.permission.INTERNET" />
                <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
                <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

         #. Add the following dependencies manually to your :file:`app/build.gradle` file:

            .. code-block:: none
               :emphasize-lines: 4, 12

                dependencies{

                    compile 'com.android.support:support-v4:24.+'
                    compile ('com.amazonaws:aws-android-sdk-auth-google:2.6.+@aar') { transitive = true; }

                    // Dependencies for the SDK Sign-in prompt UI library
                    compile 'com.android.support:appcompat-v7:24.+'
                    compile ('com.amazonaws:aws-android-sdk-auth-ui:2.6.+@aar') { transitive = true; }
                }

         #. In the Activity where you instantiate :code:`IdentityManager`, use the following imports.

            .. code-block:: none
               :emphasize-lines: 0

                import com.amazonaws.mobile.config.AWSConfiguration;
                import com.amazonaws.mobile.auth.core.IdentityManager;
                import com.amazonaws.mobile.auth.google.GoogleSignInProvider;
                import com.amazonaws.mobile.auth.core.DefaultSignInResultHandler;                                                                             ;

            In the Activity where you instantiate sign-in flow, use the following imports.

            .. code-block:: none
               :emphasize-lines: 0

                import com.amazonaws.mobile.auth.core.IdentityManager;
                import com.amazonaws.mobile.auth.ui.AuthUIConfiguration;
                import com.amazonaws.mobile.auth.ui.SignInActivity;
                import com.amazonaws.mobile.auth.google.GoogleButton;

      #. Register the Google provider with :code:`IdentityManager`

         :code:`com.amazonaws.mobile.user.IdentityManager` provides an entry point for registering identity providers and starting the authentication flow. :code:`IdentityManager` keeps track of the
         user's |COG| credentials. :code:`IdentityManager` provides methods for getting the user's
         unique |COG| identity ID and the credentials provider needed to instantiate other AWS
         clients.

         When User Sign-in is enabled :code:`IdentityManager` facilitates signing the user into the
         app and provides methods for getting information about the signed-in user.

         Prior to calling :code:`doStartupAuth`, use the following code to create an
         :code:`IdentityManager` and register your identity provider(s). Whether or not identity
         providers are added, :code:`IdentityManager` acquires an unauthenticated AWS identity that
         enables access to AWS resources that don't require authentication.

         A good practice is to instantiate :code:`IdentityManager` upon application startup, for
         instance, in the :code:`OnCreate` event of a public class that extends
         :code:`MultidexApplication`. Learn more about `MultidexApplication <https://developer.android.com/studio/build/multidex.html>`_.

         Prior to your call to :code:`doStartupAuth()`, use the following code to register
         :code:`CognitoUserPoolsSignInProvider` with the :code:`IdentityManager` as an identity
         provider.

         .. code-block:: java
            :emphasize-lines: 8, 70

              //. . .

             import com.amazonaws.mobile.config.AWSConfiguration;
             import com.amazonaws.mobile.auth.core.IdentityManager;
             import com.amazonaws.mobile.auth.goole.GoogleSignInProvider;



             /**
              * Application class responsible for initializing singletons and other common components.
              */
             public class Application extends MultiDexApplication {
                 private static final String LOG_TAG = Application.class.getSimpleName();


                 @Override
                 public void onCreate() {

             super.onCreate();
                     initializeApplication();
                     // Application initialized
                 }

                 private void initializeApplication() {

                     AWSConfiguration awsConfiguration = new AWSConfiguration(getApplicationContext());

                    // If IdentityManager is not created, create it
                    if (IdentityManager.getDefaultIdentityManager() == null) {
                            IdentityManager identityManager =
                                 new IdentityManager(getApplicationContext(), awsConfiguration);
                            IdentityManager.setDefaultIdentityManager(identityManager);
                    }

                               // Add Google as Identity Provider.
                    IdentityManager.getDefaultIdentityManager().addSignInProvider(
                         GoogleSignInProvider.class);
                    GoogleSignInProvider.setPermissions(Scopes.EMAIL, Scopes.PROFILE);


                      // . . .

                     }
             }

      #. Manage sign-in UI by calling the library provided by the SDK.

         To prompt users who are not yet signed in or to authenticate those who are already signed
         in, modify the :code:`onCreate` method of :code:`SplashActivity` and add related methods
         using the following code.

         .. code-block:: java
            :emphasize-lines: 0, 30, 56, 82, 104, 128, 154, 160

             import com.amazonaws.mobile.auth.core.DefaultSignInResultHandler;
             import com.amazonaws.mobile.auth.core.IdentityManager;
             import com.amazonaws.mobile.auth.core.IdentityProvider;
             import com.amazonaws.mobile.auth.core.StartupAuthErrorDetails;
             import com.amazonaws.mobile.auth.core.StartupAuthResult;
             import com.amazonaws.mobile.auth.core.StartupAuthResultHandler;
             import com.amazonaws.mobile.auth.core.signin.AuthException;
             import com.amazonaws.mobile.auth.ui.AuthUIConfiguration;
             import com.amazonaws.mobile.auth.ui.SignInActivity;

              @Override
             protected void onCreate(Bundle savedInstanceState) {
                 super.onCreate(savedInstanceState);
                 setContentView(R.layout.activity_splash);

                 final IdentityManager identityManager =
                         IdentityManager.getDefaultIdentityManager();

                 identityManager.doStartupAuth(this,
                     new StartupAuthResultHandler() {
                         @Override
                         public void onComplete(final StartupAuthResult authResults) {
                             if (authResults.isUserSignedIn()) {
                                 final IdentityProvider provider =
                                         identityManager.getCurrentIdentityProvider();

                                 // If the user was  signed in previously with a provider,
                                 // indicate that to them with a toast.
                                 Toast.makeText(
                                         SplashActivity.this, String.format("Signed in with %s",
                                         provider.getDisplayName()), Toast.LENGTH_LONG).show();
                                 goMain(SplashActivity.this);
                                 return;

                             } else {
                                 // Either the user has never signed in with a provider before
                                 // or refresh failed with a previously signed in provider.

                                 // Optionally, you may want to check if refresh
                                 // failed for the previously signed in provider.

                                 final StartupAuthErrorDetails errors =
                                         authResults.getErrorDetails();

                                  if (errors.didErrorOccurRefreshingProvider()) {
                                     final AuthException providerAuthException =
                                         errors.getProviderRefreshException();

                                     // Credentials for previously signed-in provider could not be refreshed
                                     // The identity provider name is available here using:
                                     //     providerAuthException.getProvider().getDisplayName()

                                 }


                                 doSignIn(IdentityManager.getDefaultIdentityManager());
                                 return;
                             }


                         }
                     }, 2000);
             }

             private void doSignIn(final IdentityManager identityManager) {

                 identityManager.setUpToAuthenticate(
                         SplashActivity.this, new DefaultSignInResultHandler() {

                             @Override
                             public void onSuccess(Activity activity, IdentityProvider identityProvider) {
                                 if (identityProvider != null) {

                                     // Sign-in succeeded
                                     // The identity provider name is available here using:
                                     //     identityProvider.getDisplayName()

                                 }

                                 // On Success of SignIn go to your startup activity
                                 activity.startActivity(new Intent(activity, MainActivity.class)
                                         .setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP));
                             }

                             @Override
                             public boolean onCancel(Activity activity) {

                                 // Return false to prevent the user from dismissing
                                 // the sign in screen by pressing back button.
                                 // Return true to allow this.

                                 return false;
                             }
                         });

                 AuthUIConfiguration config =
                         new AuthUIConfiguration.Builder()
                                                .signInButton(GoogleButton.class)
                                                // .signInButton(FacebookButton.class)
                                                // .userPools(true)
                                                .build();

                 Context context = SplashActivity.this;
                 SignInActivity.startSignInActivity(context, config);
                 SplashActivity.this.finish();
             }

             /** Go to the main activity. */
             private void goMain(final Activity callingActivity) {
                 callingActivity.startActivity(new Intent(callingActivity, MainActivity.class)
                     .setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP));
                 callingActivity.finish();
             }


   iOS - Swift
      #. Add your backend service configuration to the app.

         From the location where your |AMH| configuration file was downloaded in a previous step,
         drag :file:`awsconfiguration.json` into the folder containing your :file:`info.plist` file
         in your Xcode project.

         Select :guilabel:`Copy items if needed` and :guilabel:`Create groups`, if these options are offered.

      #. Set up the SDK component for Google sign-in by including the :file:`Podfile`.

         .. code-block:: none

             platform :ios, '9.0'

             target :'YOUR-APP-NAME' do
               use_frameworks!

                 pod 'AWSAuthUI', '~> 2.6.5'
                 pod 'AWSGoogleSignIn', '~> 2.6.5'
                 pod 'GoogleSignIn', '~> 4.0'
                 # other pods

               end

         Run :code:`pod install --repo-update` before you continue.

      #. Add Google metadata to info.plist.

         To configure your Xcode project to use Google Login, open its :file:`Info.plist` file using
         :guilabel:`Right-click > Open As > Source Code`. Add the following entry. Substitute your
         project name for the placeholder string.

         .. code-block:: xml

             <plist version="1.0">
                 <!-- . . . -->
             <key>CFBundleURLTypes</key>
             <array>
                 <dict>
                     <key>CFBundleURLSchemes</key>
                     <array>
                         <string>com.googleusercontent.apps.{xxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}</string>
                     </array>
                 </dict>
             </array>
                 <!-- . . . -->

      #. Register Google as a sign-in provider in :code:`AppDelegate`.

         .. code-block:: swift
            :emphasize-lines: 0, 20, 26

             import UIKit
             import AWSAuthCore
             import AWSGoogleSignIn

             func didFinishLaunching(
                     _application: UIApplication,
                         withOptions launchOptions: [AnyHashable: Any]?) -> Bool {

                     // Set Google login scopes before authenticating the user.
                     // Additional scopes can be added here if desired.
                     AWSGoogleSignInProvider.sharedInstance().setScopes(["profile", "openid"])

                     // Register the sign in provider instances with their unique identifier
                     AWSSignInManager.sharedInstance().register(
                         signInProvider: AWSGoogleSignInProvider.sharedInstance())

                     let didFinishLaunching:
                         Bool = AWSSignInManager.sharedInstance().interceptApplication(
                             application, didFinishLaunchingWithOptions: launchOptions)

                     if (!isInitialized) {
                         AWSSignInManager.sharedInstance().resumeSession(completionHandler: {
                             (result: Any?, error: Error?) in
                             print("Result: \(result) \n Error:\(error)")
                         })
                         isInitialized = true
                     }

                     return didFinishLaunching
             }

      #. Implement your sign-in UI using the library provided by the SDK with the
         following code.

         .. code-block:: swift
            :emphasize-lines: 0, 24, 44, 48

             import UIKit
             import AWSAuthUI
             import AWSGoogleSignIn

             class SampleViewController: UIViewController {
                 override func viewDidLoad() {
                     super.viewDidLoad()
                     // Optionally check if the user is logged in
                     // using AWSSignInManager.sharedInstance().isLoggedIn
                     presentAuthUIViewController()
                 }

                  func presentAuthUIViewController() {
                        let config = AWSAuthUIConfiguration()
                         config.addSignInButtonView(class: AWSGoogleSignInButton.self)
                         // you can use properties like logoImage, backgroundColor to customize screen
                         // config.canCancel = false // this will disallow
                         // end user to dismiss sign in screen

                         // you should have a navigation controller for your view controller
                         // the sign in screen is presented using the navigation controller

                         AWSAuthUIViewController.presentViewController(
                             with: navigationController!,  // Put your navigation controller here
                             configuration: config,
                             completionHandler: {(
                                 _ signInProvider: AWSSignInProvider, _ error: Error?) -> Void in
                                   if error == nil {
                                      DispatchQueue.main.async(execute: {() -> Void in
                                          // handle successful callback here, e.g.
                                          // pop up to show successful sign in
                                      })

                                  }
                                  else {
                                           // Take user login error
                                  }
                              })
                         }
             }




