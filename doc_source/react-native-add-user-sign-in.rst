.. _react-native-add-user-sign-in:


#######################
Add Auth / User Sign-in
#######################


.. meta::
    :description:
        Learn how to use |AMHlong| (|AMH|) to create, build, test and monitor mobile apps that are
        integrated with AWS services.

.. list-table::
   :widths: 1 6

   * - **BEFORE YOU BEGIN**

     - The steps on this page assume you have already completed the steps on :ref:`Get Started <react-native-getting-started>`.


Set Up Your Backend
===================

The AWS Mobile CLI components for user authentication include a rich, configurable  UI for sign-up and sign-in.

**To enable the Auth features**

In the root folder of your app, run:

.. code-block:: java

      awsmobile user-signin enable

      awsmobile push

Connect to Your Backend
=======================

The AWS Mobile CLI enables you to integrate ready-made sign-up/sign-in/sign-out UI from the command line.

**To add user auth UI to your app**

#. Install AWS Amplify for React Nativelibrary.

   .. code-block:: bash

       npm install --save aws-amplify-react-native

#. If you used :code:`create-react-native-app` to create your app, eject your React Native app, using the command:

   .. code-block:: bash

      npm run eject

#. Create a native bridge to the Amazon Cognito identity library with the following command:

   .. code-block:: bash

      react-native link amazon-cognito-identity-js


#. Add the following import in :file:`App.js` (or other file that runs upon app startup):

   .. code-block:: java

      import { withAuthenticator } from 'aws-amplify-react-native';

#. Then change :code:`export default App;` to the following.

   .. code-block:: java

      export default withAuthenticator(App);

To test, run :code:`npm start` or :code:`awsmobile run`.


Next Steps
==========

Learn more about the AWS Mobile :ref:`User Sign-in <user-sign-in>` feature, which uses `Amazon Cognito <http://docs.aws.amazon.com/cognito/latest/developerguide/welcome.html>`_.

Learn about :ref:`AWS Mobile CLI <aws-mobile-cli-reference>`.

Learn about `AWS Mobile Amplify <https://aws.github.io/aws-amplify>`_.

