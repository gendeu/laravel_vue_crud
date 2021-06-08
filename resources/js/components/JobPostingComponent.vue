<template>
    <div>
        <h1>Job Posting List</h1>
        <table class="table">
            <thead>
                <tr>
                <th scope="col">#</th>
                <th scope="col">Job Title</th>
                <th scope="col">Job Description</th>
                <th scope="col">Vacant</th>
                <th scope="col">Action</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in postingList" :key="item.id">
                    <th scope="row">{{ index + 1 }}</th>
                        <td>{{ item.job_name }}</td>
                        <td>{{ item.job_description }}</td>
                        <td>{{ item.vacants }}</td>
                    <td>
                        <button class="btn btn-secondary btn-sm" @click="edit(item)"><i class="fa fa-edit"></i></button>
                        <button class="btn btn-danger btn-sm" @click="remove(item, index)"><i class="fa fa-trash"></i></button>
                    </td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <td>
                    </td>
                    <td>
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="Input Job Name" v-model="form.job_name">
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="Input Job Description" v-model="form.job_description">
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <input type="number" class="form-control" placeholder="Input Vacants" v-model="form.vacants">
                        </div>
                    </td>
                    <td>
                        <div class="form-group">
                            <button class="btn btn-primary btn-sm" @click="submit"><i class="fa fa-plus"></i></button>
                        </div>
                    </td>
                </tr>
            </tfoot>
        </table>

        <!-- MODAL START -->
        <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">New message</h5>
                    <button type="button" class="btn btn-danger btn-sm" @click="closeEdit" aria-label="Close"><i class="fa fa-times"></i></button>
                </div>
                <div class="modal-body">
                    <form>
                    <div class="mb-3">
                        <label class="col-form-label">Job Name:</label>
                        <input type="text" class="form-control" placeholder="Input Job Name" v-model="formEdit.job_name">
                    </div>
                    <div class="mb-3">
                        <label class="col-form-label">Job Description:</label>
                        <textarea class="form-control" placeholder="Input Job Description" v-model="formEdit.job_description"></textarea>
                    </div>
                    <div class="mb-3">
                        <label for="message-text" class="col-form-label">Vacants:</label>
                        <input type="number" class="form-control" placeholder="Input Vacants" v-model="formEdit.vacants">
                    </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" @click="closeEdit">Close</button>
                    <button type="button" class="btn btn-primary" @click="save()">Save Changes</button>
                </div>
                </div>
            </div>
        </div>
        <!-- MODAL END -->

    </div>
</template>

<script>
export default {
    props: [
        'posting'
    ],
    data() {
        return {
            postingList: this.posting,
            form: {
                job_name: null,
                job_description: null,
                vacants: 0,
            },
            formEdit: {
                job_name: null,
                job_description: null,
                vacants: 0,
            },
            selectedId: null
        }
    },
    methods: {
        submit() {
            const vm = this;
            axios.post('/job_postings', this.form) 
            .then(function (response) {
                vm.postingList.push(response.data.data);
                vm.form = "";
            })
            .catch(function (error) {
                alert('Something went wrong.');
            });
        },
        remove(item, index) {
            const vm = this;
            axios.delete(`/job_postings/${item.id}`) 
            .then(function (response) {
                vm.postingList.splice(index, 1);
            })
            .catch(function (error) {
                alert('Something went wrong.');
            });
        },
        edit(item) {
            this.formEdit.job_name = item.job_name;
            this.formEdit.job_description = item.job_description;
            this.formEdit.vacants = item.vacants;
            this.selectedId = item.id;
            $('#editModal').modal('show');
        },
        closeEdit() {
            $('#editModal').modal('hide');
        },
        save() {
            const vm = this;
            axios.put(`/job_postings/${vm.selectedId}`, this.formEdit) 
            .then(function (response) {
                alert('Job Posting has been successfully saved!');
                location.reload();
            })
            .catch(function (error) {
                alert('Something went wrong.');
            });
        }
    }
}
</script>