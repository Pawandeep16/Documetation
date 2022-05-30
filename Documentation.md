## To Disable Student whose Fee is Unaid 

- First we have to fetch the details of the students whose fee is overdue we can do this with the following code
- Paste this code in the api.py file in the education directory of erpnext
```py
@frappe.whitelist()
def get_over_due():
    due = frappe.db.sql(f""" SELECT name,student,student_name,academic_year,due_date,outstanding_amount  FROM `tabFees` where due_date <= '{today()}' and outstanding_amount > 0 """,as_dict = True)
    return due

```
- Then we need to use this info to disable the students  and update their enable disable state .
-In the Studnet doctype there is file student_list.js ,paste the below code to operate.
```js
  get_indicator(doc, frm) {
    // customize indicator color
    frappe.call({
      method: "erpnext.education.api.get_over_due",
      callback: function (r) {
        if (r) {
          $.each(r.message, function (i, d) {
            if ((i, d.student === doc.name)) {
              frappe.call({
                method: "frappe.client.set_value",
                args: {
                  doctype: "Student",
                  name: doc.name,
                  fieldname: "enabled",
                  value: 0,
                },
                freeze: true,
                callback: function (r) {
                  frappe.msgprint(__("Student has been Disable from Access"));
                },
              });
            }
          });
        }
      },
    });

    // if (doc.enabled) {
    //   return [__("Enabled"), "blue", "enabled,=,1"];
    //   // } else if (doc.unpaid) {
    //   //   return [__("unpaid"), "red", "unpaid,=,1"];
    // } else {
    //   return [__("Disabled"), "darkgray", "disabled,=,1"];
    // }
  },
```
