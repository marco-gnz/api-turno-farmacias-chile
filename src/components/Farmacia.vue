<template>
  <div class="col-sm-3">
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">
          Farmacia <b>{{ farmacia.local_nombre }}</b>
        </h5>
        <ul class="list-group list-group-flush">
          <li class="list-group-item">
            <b>Nº</b>: {{ farmacia.local_telefono }}
          </li>
          <li class="list-group-item">
            <b>Horario</b>: {{ farmacia.funcionamiento_dia }} de
            {{ farmacia.funcionamiento_hora_apertura }} a
            {{ farmacia.funcionamiento_hora_cierre }} hrs.
          </li>
          <li class="list-group-item">
            <b>Comuna</b>: {{ farmacia.comuna_nombre }}
          </li>
          <li class="list-group-item">
            <b>Dirección</b>: <em>{{ farmacia.local_direccion }}</em>
          </li>
        </ul>
        <button type="button" class="btn btn-link">Ver mas</button>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  props: ["farmacia"],
};
</script>

<style>
</style>