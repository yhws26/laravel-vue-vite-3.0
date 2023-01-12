<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

const usuarioId = JSON.parse(document.head.querySelector('meta[name="user"]').content);

const router = useRouter();

//const onPerfil = () => {
//    router.push('/perfil/' + usuarioId);
//}

let alquileres = ref([])

onMounted(async () => {
    getAlquileres()
})

const editarAlquiler = (id) => {
    router.push('/EditarAlquiler/' + id)
}

const verFactura = (id) => {
    router.push('/verFactura/' + id)
}

const getAlquileres = async () => {

    let response = await axios.get('api/get_alquileres')
    alquileres.value = response.data.alquileres
    console.log('alquileres', alquileres.value)
}

const cambiarEstadoActivo = (id) => {
    axios.get(`/api/estado_activo_alquiler/${id}`)
        .then(() => {
            getAlquileres()
        })
        .catch((error) => {
            console.log(error.response)
        })
}

const cambiarEstadoInactivo = (id) => {
    axios.get(`/api/estado_inactivo_alquiler/${id}`)
        .then(() => {
            getAlquileres()
        })
        .catch((error) => {
            console.log(error.response)
        })
}

$(document).ready(function () {
    $("#busqueda").on("keyup", function () {
        var value = $(this).val().toLowerCase();
        $("#alquileres tr").filter(function () {
            $(this).toggle($(this).text()
                .toLowerCase().indexOf(value) > -1)
        });
    });
});

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
            <!-- Main content -->
            <div class="content">
                <div class="container-fluid">
                    <div class="row">
                        <!-- /.col-md-6 -->
                        <div class="col-lg-12">
                            <div class="card border-danger text-danger" style="height: 600px;">
                                <div class="card-header">
                                    <h3 class="card-title">Lista de Alquileres</h3>
                                    <div class="card-tools">
                                        <div class="input-group input-group-sm" style="width: 150px;">
                                            <input id="busqueda" type="text" name="table_search" class="form-control float-right"
                                                placeholder="Buscar">
                                        </div>
                                    </div>
                                </div>
                                <!-- /.card-header -->
                                <div class="card-body">
                                    <table class="table table-striped table-dark">
                                        <thead>
                                            <tr>
                                                <th>Cliente</th>
                                                <th>Vehiculo</th>
                                                <th>Fecha Desde</th>
                                                <th>Fecha Hasta</th>
                                                <th>Total</th>
                                                <th>Estado</th>
                                                <th style="width: 430px;">Acciones</th>
                                            </tr>
                                        </thead>
                                        <tbody id="alquileres">
                                            <tr v-for="alquiler in alquileres" :key="alquiler.id">
                                                <td>{{ alquiler.cliente }}</td>
                                                <td>{{ alquiler.vehiculo }}</td>
                                                <td>{{ alquiler.fecha_desde }}</td>
                                                <td>{{ alquiler.fecha_hasta }}</td>
                                                <td>{{ alquiler.precio }}</td>
                                                <td>{{ alquiler.estado }}</td>
                                                <td>
                                                    <div class="btn-group">
                                                        <button type="button" class="btn btn-default" style="background-color: white;color: black;"
                                                            @click="verFactura(alquiler.id)">Ver
                                                            Factura</button>
                                                        <div class="btn-group">
                                                            <button type="button" class="btn btn-default" style="background-color: white;color: black;"
                                                                @click="editarAlquiler(alquiler.id)">Editar</button>
                                                        </div>
                                                        <div class="btn-group">
                                                            <button type="button"
                                                                class="btn btn-default dropdown-toggle" style="background-color: white; color: black;"
                                                                data-toggle="dropdown" aria-expanded="false">Cambiar
                                                                Estado</button>
                                                            <ul class="dropdown-menu">
                                                                <li><button class="dropdown-item"
                                                                        @click="cambiarEstadoActivo(alquiler.id)">Activo</button>
                                                                </li>
                                                                <li><button class="dropdown-item"
                                                                        @click="cambiarEstadoInactivo(alquiler.id)">Inactivo</button>
                                                                </li>
                                                            </ul>
                                                        </div>
                                                    </div>
                                                </td>
                                            </tr>
                                        </tbody>
                                    </table>
                                </div>
                            </div>
                            <nav aria-label="...">
                                    <ul class="pagination float-right">
                                        <li class="page-item active">
                                        <a class="page-link" href="#" tabindex="-1" aria-disabled="true">Anterior</a>
                                        </li>
                                        <li class="page-item"><a class="page-link" href="#">1</a></li>
                                        <li class="page-item disabled" aria-current="page">
                                        <a class="page-link" href="#">2</a>
                                        </li>
                                        <li class="page-item"><a class="page-link" href="#">3</a></li>
                                        <li class="page-item">
                                        <a class="page-link" href="#">Siguiente</a>
                                        </li>
                                    </ul>
                                </nav>
                                
                                <router-link to="/CrearAlquiler" class="btn btn-primary">Nuevo alquiler</router-link>
                        </div>
                    </div>
                    <!-- /.col-md-6 -->
                </div>
                <!-- /.row -->
            </div><!-- /.container-fluid -->
        </div>
        <!-- /.content -->

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
    <!-- /.content-wrapper -->
</template>


