# Validation

--------------------------------------------------------

* [Usage](#usage)
* [Validation rules](#validation_rules)
* [Custom messages](#custom_messages)
* [Creating custom rules](#creating_custom_rules)

--------------------------------------------------------

The validation class provides a simple and consistent way of validating user input.

--------------------------------------------------------

<a id="usage"></a>

### Usage

First you need to define a set of rules you want to use to validate the input.

	$rules = array
	(
		'username' => 'required|min_length:4|max_length:20',
		'password' => 'required',
		'email'    => 'required|email',
	);

The rules defined above will make sure that the username, password and email fields are filled in. That the username is between 4 and 20 characters long, and that the email field contains a valid email address.


If all of your fields have one or more rule in common, then you can use the ```*``` shortcut.

	$rules = array
	(
		'*'        => 'required',
		'username' => 'min_length:4|max_length:20',
		'email'    => 'email',
	);

> Only non-empty fields or fields marked as required are validated.

Next you'll need to create a validation object. The first parameter is the input data you want to validate and the second is the validation rules.

	$validation = new Validate($_POST, $rules);

Now you need to check if the input data is valid using the ```successful``` method.

	if($validation->successful())
	{
		// Save to database
	}
	else
	{
		// Handle error
	}

You can also use the ```failed``` method.

	if($validation->failed())
	{
		// Handle error
	}
	else
	{
		// Save to database
	}

Retrieving errors is done using the ```errors``` method.

	$errors = $validation->errors();

Or by using the optional ```$errors``` parameter of the ```successful``` and ```failed``` methods.

	if($validation->successful($errors))
	{
		// Save to database
	}
	else
	{
		var_dump($errors);
	}

> An empty array will be returned if there are no errors.

--------------------------------------------------------

<a id="validation_rules"></a>

### Validation rules

The following validation rules are included with Mako:

| Name                     | Description                                                                                              |
|--------------------------|----------------------------------------------------------------------------------------------------------|
| required                 | Checks that the field isn't empty.                                                                       |
| min_length               | Checks that the field value is long enough (```min_length:10```).                                        |
| max_length               | Checks that the field value is short enough (```max_length:20```).                                       |
| exact_length             | Checks that the field value is of the right length (```exact_length:20```).                              |
| less_than                | Checks that the field value is less than x (```less_than:5```).                                          |
| less_than_or_equal_to    | Checks that the field value is less than or equal to x (```less_than_or_equal_to:5```).                  |
| greater_than             | Checks that the field value is greater than x (```greater_than:5```).                                    |
| greater_than_or_equal_to | Checks that the field value is greater than or equal to x (```greater_than_or_equal_to:5```).            |
| between                  | Checks that the field value is between x and y (```between:5,10```).                                     |
| match                    | Checks that the field value matches the value of another field (```match:password_confirmation```).      |
| different                | Checks that the field value is different from the value of another field (```different:old_password```). |
| regex                    | Checks that the field value matches a regex pattern (```regex:/[a-z]+/i```).                             |
| integer                  | Checks that the field value is a integer.                                                                |
| float                    | Checks that the field value is a float.                                                                  |
| natural                  | Checks that the field value is a natural.                                                                |
| natural_non_zero         | Checks that the field value is a natural non zero.                                                       |
| hex                      | Checks that the field value is valid HEX.                                                                |
| alpha                    | Checks that the field value only contains valid alpha characters.                                        |
| alpha_unicode            | Checks that the field value only contains valid alpha unicode characters.                                |
| alphanumeric             | Checks that the field value only contains valid alphanumeric characters.                                 |
| alphanumeric_unicode     | Checks that the field value only contains valid alphanumeric unicode characters.                         |
| alpha_dash               | Checks that the field value only contains valid alphanumeric, dash and underscore characters.            |
| alpha_dash_unicode       | Checks that the field value only contains valid alphanumeric unicode, dash and underscore characters.    |
| email                    | Checks that the field value is a valid email address (uses PHP's filter_var function).                   |
| email_domain             | Checks that the field value contains a valid MX record.                                                  |
| ip                       | Checks that the field value is an IP address (uses PHP's filter_var function).                           |
| url                      | Checks that the field value is a valid URL (uses PHP's filter_var function).                             |
| in                       | Checks that the field value contains one of the given values (```in:foo,bar,baz```).                     |
| not_in                   | Checks that the field value does not contain one of the given values (```not_in:foo,bar,baz```).         |
| date                     | Checks that the field value is a valid date (```date:Y-m-d```).                                          |
| before                   | Checks that the field value is a valid date before the provided date (```before:Y-m-d,2012-09-25```).    |
| after                    | Checks that the field value is a valid date after the provided date (```after:Y-m-d,2012-09-25```).      |
| token                    | Checks that the field value matches a valid [security token]().                                          |
| uuid                     | Checks that the field value matches a valid [uuid]().                                                    |
| unique                   | Checks that the field value doesn't exist in the database (```unique:users,email```).                    |
| exists                   | Checks that the field value exist in the database (```exists:users,email```).                            |

--------------------------------------------------------

<a id="custom_messages"></a>

### Custom messages

All error messages are defined in the ```app/i18n/*/strings/validate.php``` language file.

Adding custom field specific error messages can be done using the ```overrides.messages``` array:

	'overrides' => array
	(
		'messages' => array
		(
			'username' => array
			(
				'required' => 'You need a username!',
			),
		),
	),

You can also add custom field name translations using the ```overrides.fieldnames``` array:

	'overrides' => array
	(
		'fieldnames' => array
		(
			'email' => 'email address',
		),
	),

--------------------------------------------------------

<a id="creating_custom_rules"></a>

### Creating custom rules

You can also register your own custom validation rules using the ```registerValidator``` method.

	Validate::registerValidator('myrule', function($input, array $parameters)
	{
		return $input === 'foo';
	});

Rules defined in a package should be prefixed with the package name followed by two colons.

	Validate::registerValidator('mypackage::myrule', function($input, array $parameters)
	{
		return $input === 'foo';
	});