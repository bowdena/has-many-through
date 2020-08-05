# README

This is a very simple Proof of concept build
for has_many, through in rails 6 because I often 
forget exactly how to make this work as I trip
over the view component of the scaffold.

I rediscovered this resource which helped to put
me on the right path: https://learn.co/lessons/has-many-through-forms-rails

---
The instructions to implement this using the 
scaffolding commands are listed below as well
for future reference.

The model is fairly intuitive:
- Doctor
- Appointment
- Patient

1. A doctor can have many patients through appointments; and
2. A patient can have many doctors through appointments.

```ruby
rails g scaffold Doctor doctor_name:string
rails g scaffold Patient patient_name:string
rails g scaffold Appointment doctor:references patient:references
rails db:migrate
```

Then, update the models:
```ruby
class Doctor < ApplicationRecord
  has_many :appointments, dependent: :destroy
  has_many :patients, through: :appointments
end

class Patient < ApplicationRecord
    has_many :appointments, dependent: :destroy
    has_many :doctors, through: :appointments
end

class Appointment < ApplicationRecord
  belongs_to :doctor
  belongs_to :patient
end
```

Update the view and allow the doctor to specify
the patients from the http://localhost:3000/doctors/new :
```ruby
app/views/doctors/_form.html.erb
 <%= form.collection_check_boxes :patient_ids, Patient.all, :id, :patient_name %>
```

Finally, update the controller to allow the 
items to pass through
```ruby
def doctor_params
      params.require(:doctor).permit(:doctor_name, patient_ids:[])
end
```