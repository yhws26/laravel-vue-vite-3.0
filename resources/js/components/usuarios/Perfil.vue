<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router'

let form = ref({
    id: '',
    name: '',
    apellido: '',
    cedula: '',
    fecha_nacimiento: '',
    numero_licencia: '',
    fecha_vencimiento: '',
    foto_perfil: '',
    foto_licencia: ''

})

onMounted(async () => {
    getPerfil()
})

const usuarioId = JSON.parse(document.head.querySelector('meta[name="user"]').content);

const router = useRouter();

const onPerfil = () => {
    router.push('/perfil/' + usuarioId);
}

const props = defineProps({
    id: {
        type: String,
        default: ''
    }
})

const getPerfil = async () => {
    let response = await axios.get(`/api/get_perfil/${props.id}`)
    form.value = response.data.usuario
}

const getFotoPerfil = () => {
    let foto_perfil = "/upload/image.png"
    if (form.value.foto_perfil != null) {
        if (form.value.foto_perfil.indexOf('base64') != -1) {
            foto_perfil = form.value.foto_perfil;
        } else {
            foto_perfil = '/upload/'+form.value.foto_perfil;
        }
        return foto_perfil
    }

}

const getFotoLicencia = () => {
    let foto_licencia = '/upload/image.png';
    if (form.value.foto_licencia) {
        if (form.value.foto_licencia.indexOf('base64') != -1) {
            foto_licencia = form.value.foto_licencia;
        } else {
            foto_licencia = '/upload/' + form.value.foto_licencia;
        }
    }
    return foto_licencia
}

const actualizarFotoPerfil = (e) => {

    let file = e.target.files[0];
    let reader = new FileReader();
    let limit = 1024 * 1024 * 2;

    if (file['size'] > limit) {
        return false
    }

    reader.onloadend = (file) => {
        form.value.foto_perfil = reader.result;
    }

    reader.readAsDataURL(file);
}

const actualizarFotoLicencia = (e) => {
    let file = e.target.files[0];
    let reader = new FileReader();
    let limit = 1024 * 1024 * 2;
    if (file['size'] > limit) {
        return false
    }
    reader.onloadend = (file) => {
        form.value.foto_licencia = reader.result;
    }

    reader.readAsDataURL(file);
}

const actualizarPerfil = () => {
    console.log(form.value.foto_perfil)
    const formData = new FormData();
    formData.append('name', form.value.name);
    formData.append('apellido', form.value.apellido);
    formData.append('cedula', form.value.cedula);
    formData.append('fecha_nacimiento', form.value.fecha_nacimiento);
    formData.append('numero_licencia', form.value.numero_licencia);
    formData.append('fecha_vencimiento', form.value.fecha_vencimiento);
    formData.append('foto_perfil', form.value.foto_perfil);
    formData.append('foto_licencia', form.value.foto_licencia);
    axios.post(`/api/actualizar_perfil/${form.value.id}`, formData)
        .then((response) => {
            form.value.name = '',
                form.value.apellido = '',
                form.value.cedula = '',
                form.value.fecha_nacimiento = '',
                form.value.numero_licencia = '',
                form.value.fecha_vencimiento = '',
                form.value.foto_perfil = '',
                form.value.foto_licencia = '',

                router.push('/home')
        })
        .catch((error) => {
            console.log(error.response)
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
                        <div class="col-lg-6">
                            <div class="card border-danger">
                                <div class="card-header">
                                    <h3 class="card-title">Fotos</h3>
                                </div>
                                <!-- form -->
                                <form>
                                    <div class="card-body">
                                        <div class="form-group">
                                            <label for="exampleInputFile">Foto de perfil</label>
                                            <div>
                                                <img :src="getFotoPerfil()"
                                                    style="height: 150px; width: 150px" />
                                            </div>
                                            <div class="custom-file">
                                                <input type="file" class="form-control form-control-sm"
                                                    @change="actualizarFotoPerfil">
                                                <label class="form-label" for="exampleInputFile"></label>
                                            </div>
                                            
                                        </div>
                                        <div class="form-group">
                                            <label for="exampleInputFile">Foto de licencia</label>
                                            <div
                                                style="">
                                                <img :src="getFotoLicencia()" alt=""
                                                    style="height: 150px; width: 300px" />
                                            </div>
                                            <div class="custom-file">
                                                <input type="file" class="form-control form-control-sm"
                                                    @change="actualizarFotoLicencia">
                                                <label class="form-label" for="exampleInputFile"></label>
                                            </div>
                                        </div>
                                    </div>
                                    <!-- /.card-body -->
                                </form>
                            </div>
                        </div>
                        <!-- /.col-md-6 -->
                        <div class="col-lg-6">
                            <div class="card border-danger">
                                <div class="card-header">
                                    <h3 class="card-title">Formulario</h3>
                                </div>
                                <!-- /.card-header -->
                                <!-- form start -->
                                <form>
                                    <div class="card-body">
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-5">Nombre</label>
                                            <div class="col-sm-7">
                                                <input type="text" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.name">
                                            </div>
                                            
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-5">Apellido</label>
                                            <div class="col-sm-7">
                                                <input type="text" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.apellido">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-5">Cedula</label>
                                            <div class="col-sm-7">
                                                <input type="number" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.cedula">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-5">Fecha de Nacimiento</label>
                                            <div class="col-sm-7">
                                                <input type="date" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.fecha_nacimiento">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-5">Numero de licencia de conducir</label>
                                            <div class="col-sm-7">
                                                <input type="number" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.numero_licencia">
                                            </div>
                                        </div>
                                        <div class="form-group row">
                                            <label for="exampleInputEmail1" class="col-sm-5">Fecha de vencimiento</label>
                                            <div class="col-sm-7">
                                                <input type="date" class="form-control" id="exampleInputEmail1"
                                                placeholder="" v-model="form.fecha_vencimiento">
                                            </div>
                                        </div>
                                    </div>
                                    <!-- /.card-body -->
                                    <div class="card-footer">
                                        <button type="button" class="btn btn-primary btn-block"
                                            @click="actualizarPerfil()">Actualizar Datos</button>
                                            <div class="overlay dark">
                                            <i class="fas fa-2x fa-sync-alt fa-spin"></i>
                                            </div>
                                    </div>
                                </form>
                            </div>
                        </div>

                        <div class="content-header">
                            <div class="container-fluid">
                                <div class="row mb-2">
                                    <div class="col-sm-6">
                                    </div><!-- /.col -->
                                </div><!-- /.row -->
                            </div><!-- /.container-fluid -->
                        </div>
                        <div class="content-header">
                            <div class="container-fluid">
                                <div class="row mb-2">
                                    <div class="col-sm-6">
                                    </div><!-- /.col -->
                                </div><!-- /.row -->
                            </div><!-- /.container-fluid -->
                        </div>
                        
                        <div class="col-lg-8">
                            <div class="card bg-default">
                                <div class="card-header d-flex justify-content-center p-3" style="">
                                    <h3 class="card-title">Cambiar Contrase??a</h3>
                                </div>
                                <!-- form -->
                                <form>
                                    <div class="card-body d-flex justify-content-center">
                                        <div class="form-group m-4">
                                            <label for="exampleInputPassword1">Contrase??a Actual</label>
                                            <input type="password" class="form-control" id="exampleInputPassword1"
                                                placeholder="">
                                        </div>
                                        <div class="form-group m-4">
                                            <label for="exampleInputPassword1">Nueva Contrase??a</label>
                                            <input type="password" class="form-control" id="exampleInputPassword1"
                                                placeholder="">
                                        </div>
                                        <div class="form-group m-4">
                                            <label for="exampleInputPassword1">Confirmar Contrase??a</label>
                                            <input type="password" class="form-control" id="exampleInputPassword1"
                                                placeholder="">
                                        </div>
                                    </div>
                                    <div class="card-footer d-flex justify-content-center p-3">
                                        <button type="submit" class="btn btn-secondary">Actualizar Contrase??a</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                        <div class="mb-5"></div>
                    </div>
                </div>
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
    <!--
    <div id="actualizar-datos" class="container">
        <div id="grid" class="row">
            <div id="espacio-foto" class="col card">
                <div class="mb ">
                    <img :src="getFotoPerfil()" class="row img-thumbnail foto-perfil" style="width: 150px;">
                    <label for="foto_perfil" class="form-label label-foto-perfil">Subir Foto de Perfil</label>
                    <input class="form-control" type="file" id="foto_perfil" name="foto_perfil"
                        v-on:change="actualizarFotoPerfil">
                </div>

                <div class="mb espacio-licencia">
                    <img :src="getFotoLicencia()" class="row img-thumbnail foto-licencia" style="width: 321px;">
                    <label for="foto_licencia" class="form-label label-foto-perfil">Subir Foto de Licencia</label>
                    <input class="form-control" type="file" id="foto_licencia" name="foto_licencia"
                        v-on:change="actualizarFotoLicencia">
                </div>
            </div>

            <div id="espacio-texto" class="col card">
                <div class="mb-3">
                    <label for="name" class="form-label">Nombre</label>
                    <input type="text" class="form-control" id="name" name="name" v-model="form.name">
                </div>

                <div class="mb-3">
                    <label for="apellido" class="form-label">Apellido</label>
                    <input type="text" class="form-control" id="apellido" name="apellido" v-model="form.apellido">
                </div>

                <div class="mb-3">
                    <label for="cedula" class="form-label">Cedula</label>
                    <input type="number" class="form-control" id="cedula" name="cedula" v-model="form.cedula">
                </div>

                <div class="mb-3">
                    <label for="fecha_nacimiento" class="form-label">Fecha de Nacimiento</label>
                    <input type="date" class="form-control" id="fecha_nacimiento" name="fecha_nacimiento"
                        v-model="form.fecha_nacimiento">
                </div>

                <div class="mb-3">
                    <label for="numero_licencia" class="form-label">Numero de Licencia de Conducir</label>
                    <input type="number" class="form-control" id="numero_licencia" name="numero_licencia"
                        v-model="form.numero_licencia">
                </div>

                <div class="mb-3">
                    <label for="fecha_vencimiento" class="form-label">Fecha de Vencimiento</label>
                    <input type="date" class="form-control" id="fecha_vencimiento" name="fecha_vencimiento"
                        v-model="form.fecha_vencimiento">
                </div>
            </div>

            <div id="boton-datos">
                <button class="btn btn-success bg-primary" @click="actualizarPerfil()">Actualizar Datos</button>
            </div>
        </div>
    </div>


    <div class="container" id="actualizar-contrasena">
        <h1>Cambiar Contrase??a</h1>
        <form action="{{ route('update-password') }}" method="POST">
            @csrf

            <div id="grid" class="row-2 d-flex justify-content-center">
                <div id="espacio-contrasena">
                    <div class="d-flex flex-row">
                        <div>
                            <label for="oldPasswordInput" class="pe-2">Contrase??a Actual</label>
                            <input name="old_password" type="password"
                                class="@error('old_password') is-invalid @enderror me-2" id="oldPasswordInput">
                            @error('old_password')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        <div>
                            <label for="newPasswordInput" class="pe-2">Nueva Contrase??a</label>
                            <input name="new_password" type="password"
                                class="@error('new_password') is-invalid @enderror me-2" id="newPasswordInput">
                            @error('new_password')
                            <span class="text-danger">{{ $message }}</span>
                            @enderror
                        </div>
                        <div>
                            <label for="confirmNewPasswordInput" class="pe-2">Confirmar Contrase??a</label>
                            <input name="new_password_confirmation" type="password" class="me-2"
                                id="confirmNewPasswordInput">
                        </div>
                    </div>
                </div>
            </div>
        </form>
    </div>
    <div class="card-footer" id="boton-datos">
        <button class="btn btn-success bg-primary">Actualizar Contrase??a</button>
    </div>-->
</template>
