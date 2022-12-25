 yarn create vite react-formik --template react
 
 yarn add formik
 yarn add yup

## Yup:

```jsx
import * as yup from "yup";
const FORM_VALIDATION_SCHEMA = yup.object({
  name: yup.string().required("error message"),
  email: yup.string().email("email message").required("error message"),
  channel: yup.string().required("error message")
})

 const formik = useFormik({
  ...
  // replace validate
  validationSchema: FORM_VALIDATION_SCHEMA
 })
```

## useFormik
import { useFormik } from Formik

 Formstate
 objectLiterals 

```jsx
 const formik = useFormik({
  initialValues: {
    name: "",
    email: "",
    channel: ""
  },
  onSubmit: values => {
  },
  validate: values => {
    let error = {}
    return errors;
  }
 })
```
```jsx
<form onSubmit={formik.onSubmit}>
<input name="name" 
  // value = {formik.values.name} 
  // onChange = {formik.handleChange} 
  // onBlur= {formik.handleBlur}

  // replace the above 3 lines with
  {...formik.getFieldProps("name")}
/>
<button type="submit">
```
name should be same as initialValues obj keys 
name should be same as error obj keys for validate

```jsx 
formik.values
formik.handleChange
formik.onSubmit
formik.errors = {
  name: "",
  email: "",
  channel: ""
}
formik.handleBlur
formik.touched = {
  name: true,
  email: true,
  channel: true,
}

{...formik.getFieldProps("name")}
```

## Formik, Form, Field

```jsx
import { Formik, Form, Field, ErrorMessage } from Formik
<Formik
  initialValues={formValues || initialValues}
  validationSchema={FORM_VALIDATION_SCHEMA}
  onSubmit={onSubmit}
>
<Form> is formik wrapper around html <form>
<Field as="input" name="name" type="text"> 
<ErrorMessage name="name" component={CustomErrorComponent}/>

func CustomErrorComponent(prop){
  return(
    <div className="error">
    {props.children}
    </div>
  )
}
```
Form => automatically hooks onsubmit
Field 
  => hookup formik to files, requires only name & type
  => as prop textarea, input, checkbox etc 
ErrorMessage 
  => abstracts error message component
  => component wraps the error message 

## Render props pattern
```jsx
<Field name='address'>
  {({ field, form, meta }) => {
    return (
      <div>
        <input type='text' {...field} />
        {meta.touched && meta.error ? (
          <div>{meta.error}</div>
        ) : null}
      </div>
    )
  }}
</Field>

<ErrorMessage name='email'>
  {error => <div className='error'>{error}</div>}
</ErrorMessage>
```

## nested values

```jsx
const initialValues = {
  social: { facebook: '', twitter: '' }
}

<Field type='text' id='facebook' name='social.facebook' />
<Field type='text' id='twitter' name='social.twitter' />

```

## Arrays values, FieldArray

```jsx
const initialValues = {
  phoneNumbers: ['', ''], phNumbers: ['']
}

import { FieldArray } from Formik

<Field type='text' id='primaryPh' name='phoneNumbers[0]' />
<Field type='text' id='secondaryPh' name='phoneNumbers[1]' />

<FieldArray name='phNumbers'>
  {fieldArrayProps => {
    const { push, remove, form } = fieldArrayProps
    const { values } = form
    const { phNumbers } = values
    return (
      <div>
        {phNumbers.map((phNumber, index) => (
          <div key={index}>
            <Field name={`phNumbers[${index}]`} />
            {index > 0 && (
              <button type='button'  ={() => remove(index)}> - </button>
            )}
          </div>
        ))}
        <button type='button' onClick={() => push('')}> + </button>
      </div>
    )
  }}
</FieldArray>
```

## FastField

```jsx
import { FastField } from Formik

replace Field with FastField
optimized & should be independent of all other component.
use with caution.
```

## Validation &  Field Level Validation
 error object is populated
 1. on interaction with a field
 2. on blur out
 3. on submission

validateOnChange={false}
validateOnBlur={false}
validateOnMount

```jsx
<Formik
  validateOnChange={false}
  validateOnBlur={false}
  validateOnMount
>

const validateComments = value => {
  let error
  if (!value) {
    error = 'Required'
  }
  return error
}

<Field as='textarea' id='comments' name='comments' validate={validateComments} />

<Formik
  ...
  >
    {formik => {
      console.log('Formik props', formik)
      return (
        <Form>
          <Field type='text' id='comments' name='comments' />
          <ErrorMessage name='comments' component={TextError} />
          <button type='button' onClick={() => formik.validateField('comments')}>
            Validate comments
          </button>
        <Form />
      )
    }}
</Formik>
```
formik.validateField('comments')
formik.setFieldTouched('comments')}
formik.validateForm()
formik.setTouched({ name: true, email: true, channel: true, comments: true })


## Submit button, Submitting & reset
formik.isValid
formik.validateOnMount
formik.dirty => any one form field is changed

formik.isSubmitting => on validation pass/ fail isSubmitting is set to true

- submitProps

```jsx
<button type='submit' disabled={!formik.isValid && !formik.dirty}> Submit </button>

// submitting

const onSubmit = (values, submitProps) => {
  submitProps.setSubmitting(false)
  // reset with submitProps
  submitProps.resetForm()
}
<button type='submit' disabled={!formik.isValid || formik.isSubmitting} > Submit </button>

// reset with type
<button type='reset'>Reset</button>

```

## setFieldValue

```jsx
// import DateView from 'react-datepicker'

<Field name={name}>
  {({ form, field }) => {
    const { setFieldValue } = form
    const { value } = field
    return (
      <DateView
        id={name}
        {...field}
        {...rest}
        selected={value}
        onChange={val => setFieldValue(name, val)}
      />
    )
  }}
</Field>
  )
}
```