<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

const usuarioId = JSON.parse(document.head.querySelector('meta[name="user"]').content);

const router = useRouter();

//const onPerfil = () => {
//    router.push('/perfil/' + usuarioId);
//}

let clientes = ref([])
let vehiculos = ref([])
let alquiler = ref([])

let form = ref({
    id: '',
    cliente: '',
    vehiculo: '',
    fecha_desde: '',
    fecha_hasta: '',
    precio: '',
    estado: ''
})

onMounted(async () => {
    getDatos()
})

const props = defineProps({
    id: {
        type: String,
        default: ''
    }
})

const getDatos = async () => {

    await axios.get('/api/get_usuarios')
        .then(function (response) {
            clientes.value = response.data.usuarios
        })

    await axios.get('/api/get_vehiculos')
        .then(function (response) {
            vehiculos.value = response.data.vehiculos
        })

    await axios.get(`/api/get_alquiler/${props.id}`)
        .then(function (response) {
            alquiler.value = response.data.alquiler
            form.value = response.data.alquiler
        })
    console.log('usuarios', clientes.value)
    console.log('vehiculos', vehiculos.value)
    console.log('alquileres', alquiler.value)
}

const EditarAlquiler = () => {
    const formData = new FormData();
    formData.append('cliente', form.value.cliente);
    formData.append('vehiculo', form.value.vehiculo);
    formData.append('fecha_desde', form.value.fecha_desde);
    formData.append('fecha_hasta', form.value.fecha_hasta);
    formData.append('precio', form.value.precio);
    formData.append('estado', form.value.estado);

    axios.post(`/api/editar_alquiler/${form.value.id}`, formData)
        .then((response) => {
            form.value.cliente = '',
                form.value.vehiculo = '',
                form.value.fecha_desde = '',
                form.value.fecha_hasta = '',
                form.value.precio = '',
                form.value.estado = '',
                router.push('/ListaAlquilados')
        })
        .catch((error) => {
            console.log(error.response)
        })
}

</script>

<template>
    <div class="wrapper">
        <!-- Main Sidebar Container -->
        <aside class="main-sidebar bg-dark sidebar-dark-primary elevation-4">
            <!-- Logo -->
            <router-link to="/home" class="brand-link">
                <img src="../../../../public/img/AdminLTELogo.png" alt="RentsUsLogo" class="brand-image img-circle elevation-3"
                    style="opacity: .8">
                <span class="brand-text font-weight-light fs-4"><a href="/home" style="text-decoration: none; color: white;">RentsUs</a></span>
            </router-link>
            <!-- Sidebar -->
            <div class="sidebar">
                <!-- Sidebar Menu -->
                <nav class="mt-2">
                    <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu"
                        data-accordion="false">
                        <li class="nav-item" style="border-radius: 7px">
                            <router-link to="/usuarios" class="nav-link">
                                <i class="fas fa-user-circle" style="padding-top: 10px; padding-bottom: 10px;"></i>
                                <p> Usuarios </p>
                            </router-link>
                        </li>
                        <li class="nav-item" style="border-radius: 7px; margin: 10px 0">
                            <a @click="onPerfil" class="nav-link" style="cursor: pointer;">
                                <i class="fas fa-address-card" style="padding-top: 10px; padding-bottom: 10px"></i>
                                <p> Perfil </p>
                            </a>
                        </li>
                        <li class="nav-item" style="border-radius: 7px; margin-bottom: 10px;">
                            <router-link to="/listaVehiculos" class="nav-link" style="cursor: pointer;">
                                <i class="fas fa-car-side" style="padding-top: 10px; padding-bottom: 10px;"></i>
                                <p> Vehiculos </p>
                            </router-link>
                        </li>
                        <li class="nav-item" style="border-radius: 7px">
                            <router-link to="/ListaAlquilados" class="nav-link" style="cursor: pointer;">
                                <i class="fas fa-car" style="padding-top: 10px; padding-bottom: 10px;"></i>
                                <p>
                                    Vehiculos alquilados
                                </p>
                            </router-link>
                        </li>
                    </ul>
                </nav>
                <!-- /.sidebar-menu -->
            </div>
            <!-- Dropdown de configuracion -->
            <div class="dropdown"  style="position: fixed;margin: 500px 0 0 30px;">
                <a href="#" class="d-flex align-items-center text-white text-decoration-none dropdown-toggle" id="dropdownUser1" data-bs-toggle="dropdown" aria-expanded="false">
                    <img src="../../../../public/img/avatar.png" alt="" width="32" height="32" class="rounded-circle me-2">
                    <strong>Config</strong>
                </a>
                <ul class="dropdown-menu dropdown-menu-dark text-small shadow" aria-labelledby="dropdownUser1">
                    <li><a class="dropdown-item" href="#">Configuraciones</a></li>
                    <li><a class="dropdown-item" href="#">Ubicacion</a></li>
                    <li><hr class="dropdown-divider"></li>
                    <li><a class="dropdown-item" href="#">Soporte</a></li>
                </ul>
            </div>
        </aside>
        <!-- Content Wrapper. Contains page content -->
        <div class="content-wrapper">
            <!-- Content Header (Page header) -->
            <div class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                        </div><!-- /.col -->
                    </div><!-- /.row -->
                </div><!-- /.container-fluid -->
            </div>
            <!-- /.content-header -->
            <router-link to="/ListaAlquilados" class="btn btn-primary" style="margin-left: 20px; margin-bottom: 20px;"><i class="fas fa-arrow-left"></i></router-link>
            <!-- Main content -->
            <div class="content">
                <div class="container-fluid">
                    <div class="row">
                        <!-- /.col-md-6 -->
                        <div class="col-lg-12">
                            <div class="card border-danger">
                                <div class="card-header">
                                    <h3 class="card-title">Editar Alquiler</h3>
                                </div>
                                <!-- /.card-header -->
                                <!-- form start -->
                                <form>
                                    <div class="card-body">
                                        <div class="form-group">
                                            <label for="exampleInputEmail1">Cliente</label>
                                            <div class="input-group">
                                                <select class="custom-select" v-model="form.cliente">
                                                    <option v-for="cliente in clientes" :key="cliente.id">{{
        cliente.name
}}</option>
                                                </select>
                                            </div>
                                        </div>

                                        <div class="form-group">
                                            <label for="exampleInputEmail1">Vehiculo</label>
                                            <div class="input-group">
                                                <select class="custom-select" v-model="form.vehiculo">
                                                    <option v-for="vehiculo in vehiculos" :key="vehiculo.id">{{
        vehiculo.marca
}} {{ vehiculo.modelo }}</option>
                                                </select>
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label for="exampleInputEmail1">Fecha Desde</label>
                                            <input type="date" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.fecha_desde">
                                        </div>
                                        <div class="form-group">
                                            <label for="exampleInputEmail1">Fecha Hasta</label>
                                            <input type="date" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.fecha_hasta">
                                        </div>
                                        <div class="form-group">
                                            <label for="exampleInputEmail1">Precio</label>
                                            <input type="number" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.precio">
                                        </div>
                                        <div class="form-group">
                                            <label for="exampleInputEmail1">Estado</label>
                                            <select class="custom-select" v-model="form.estado">
                                                <option>Activo</option>
                                                <option>Inactivo</option>
                                            </select>
                                        </div>
                                    </div>
                                    <!-- /.card-body -->
                                    <div class="card-footer">
                                        <button type="button" class="btn btn-primary"
                                            @click="EditarAlquiler()">Guardar</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                    <!-- /.row -->
                </div><!-- /.container-fluid -->
            </div>
            <!-- /.content -->
        </div>
        <!-- /.content-wrapper -->

        <!-- Control Sidebar -->
        <aside class="control-sidebar control-sidebar-dark">
            <!-- Control sidebar content goes here -->
            <div class="p-3">
                <h5>Title</h5>
                <p>Sidebar content</p>
            </div>
        </aside>
        <!-- /.control-sidebar -->

        <!-- Main Footer -->
        <footer class="main-footer">
            <div class="float-right d-none d-sm-inline">Alquiler de Vehiculos | RentsUs</div>
            <strong>Copyright &copy; 2023 <a href="https://youtu.be/UTH1VNHLjng">RentsUs</a>.</strong> Todoslos derechos reservados.
            <img src="../.../../../../../public/img/adobecolor.png" class="" style="width: 150px; margin-left: 100px;">
        </footer>
    </div>
</template>
