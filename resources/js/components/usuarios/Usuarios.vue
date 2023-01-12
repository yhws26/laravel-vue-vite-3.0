<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

const usuarioId = JSON.parse(document.head.querySelector('meta[name="user"]').content);

const router = useRouter();

const onPerfil = () => {
    router.push('/perfil/' + usuarioId);
}

let usuarios = ref([])

onMounted(async () => {
    getUsuarios()
})

const getUsuarios = async () => {

    let response = await axios.get('api/get_usuarios')
    usuarios.value = response.data.usuarios
    console.log('usuarios', usuarios.value)
}

const eliminarUsuario = (id) => {
    axios.get(`/api/eliminar_usuario/${id}`)
        .then(() => {
            getUsuarios()
        })
        .catch(() => {
            console.log()
        })
}

</script>

<template>
    <div class="wrapper">
        <!-- Sidebar Container -->
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

        <!-- Content Wrapper -->
        <div class="content-wrapper">

            <!-- Content Header -->
            <div class="content-header">
                <div class="container-fluid">
                    <div class="row mb-2">
                        <div class="col-sm-6">
                        </div>
                    </div>
                </div>
            </div>
            <!-- /.content-header -->

            <!-- Main content -->
            <div class="content">
                <div class="container-fluid">
                    <div class="row">
                        <div class="col-lg-12">
                            <div class="card border-danger text-danger" style="height: 600px;">
                                <div class="card-header">
                                    <h3 class="card-title">Usuarios</h3>
                                    <router-link to="" class="btn btn-primary" style="margin-left: 800px;"><i class="fas fa-plus"></i></router-link>
                                </div>
                                <!-- /.card-header -->
                                <div class="card-body">
                                    <table class="table table-striped table-dark">
                                        <thead class="thead-dark">
                                            <tr>
                                                <th>Nombre</th>
                                                <th>Apellido</th>
                                                <th>Cedula</th>
                                                <th style="width: 430px;">Opciones</th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <tr v-for="usuario in usuarios" :key="usuario.id">
                                                <td>{{ usuario.name }}</td>
                                                <td>{{ usuario.apellido }}</td>
                                                <td>{{ usuario.cedula }}</td>
                                                <td>
                                                    <div class="btn-group">
                                                        <button type="button" class="btn btn-danger"
                                                            @click="eliminarUsuario(usuario.id)">Eliminar</button>
                                                        <div class="btn-group">
                                                            <button type="button"
                                                                class="btn btn-primary dropdown-toggle"
                                                                data-toggle="dropdown" aria-expanded="false">Cambiar
                                                                Estado</button>
                                                            <ul class="dropdown-menu" style="">
                                                                <li><a class="dropdown-item" href="#">Activo</a></li>
                                                                <li><a class="dropdown-item" href="#">Inactivo</a></li>
                                                            </ul>
                                                        </div>
                                                        <button type="button" class="btn btn-primary">Editar
                                                            Rol</button>
                                                        <button type="button" class="btn btn-primary"
                                                            @click="onPerfil()">Ver Perfil</button>
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
                                    <li class="page-item disabled"><a class="page-link" href="#">1</a></li>
                                    <li class="page-item" aria-current="page">
                                    <a class="page-link" href="#">2</a>
                                    </li>
                                    <li class="page-item"><a class="page-link" href="#">3</a></li>
                                    <li class="page-item">
                                    <a class="page-link" href="#">Siguiente</a>
                                    </li>
                                </ul>
                            </nav>
                        </div>
                        <!-- /.col-md-6 -->
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
