# Setting up this playbook in Ansible Tower
1. Log into tower
2. Create a new project to point to the playbook for ServiceNow
    a. Use `SCM TYPE` of `Git`
    b. Set the `SCM URL` to `https://github.com/jnpacker/ansible-tower-samples.git`
    c. Check the `UPDATE REVISION ON LAUNCH` option and save
3. Create a NEW `Job Template`
    a. Fill in `NAME` with `Service Now App Update` this name must match what is in your `ansibleJob` object under `spec.job_template_name`
    b. Choose `JOB TYPE` of `Run`
    c. Choose `INVENTORY` of `Demo Inventory` this allows it to just run locally
    d. Choose `PROJECT`, use the project created in step (2) above
    e. Choose `PLAYBOOK` and select `create_snow_ticket.yml`
    f. In the `EXTRA VARIABLES` section add the following and fill in the appropriate Service NOW values
    ```
    ---
    #snow_record variables

    sn_username: admin
    sn_password: SNow_PASSWORD_FOR_ADMIN
    sn_instance: SNow_INSTANCE_ID

    #data variables

    sn_severity: 2
    sn_priority: 2
    ```
    g. `SAVE` the template
# Running the playbook on OpenShift with Advanced Cluster Management
1. Login and choose a namespace to work in (create one if needed)
2. In the namespace where you will create the `snow-ansiblejob.yaml` resource, create a secret with the name `toweraccess`. Here is an example secret you can apply after filling in the URL and token (both need to be base64 encoded)
```yaml
apiVersion: v1
data:
  host: BASE64_ENCODED_TOWER_URL
  token: BASE64_ENCODED_TOWER_TOKEN
kind: Secret
metadata:
  name: toweraccess
type: Opaque
```
3. You can now execute the job by create the ansibleJob resource in your OpenShift
    ```
    # Command:
    oc apply -f ./snow-ansiblejob.yaml
    ```

# Viewing your results
1. Log into your ServiceNow instance
2. In the top left search bar, type `change` and select `Open` from the list in the left hand navigation bar
3. In the `Search` on the `Change Request` header, select `for text` in the drop down, and type `Pacman` in the text field and then press `enter`
<img src="snow-change-search.png">
4. You will see all related change tickets that were openned.  The ticket includes a list of clusters where the application was updated.

