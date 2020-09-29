<template>
   <div id="map"></div>
</template>
<script src="https://api-maps.yandex.ru/2.1/?lang=ru_RU&amp;apikey=a354441d-8b7b-4892-9950-6989fc996eee" type="text/javascript"></script>



<script>
export default {
  data(){
    return {
       message: 'Hello to YANDEX MAP!',
      address: ['Алматы, Алимжанова 51'],
  
  
    }
    
  },
  
  created() {
   
  var my_end_point=this.address[0];
      function init () {
      /**
       * Создаем мультимаршрут.
       * Первым аргументом передаем модель либо объект описания модели.
       * Вторым аргументом передаем опции отображения мультимаршрута.
       * @see https://api.yandex.ru/maps/doc/jsapi/2.1/ref/reference/multiRouter.MultiRoute.xml
       * @see https://api.yandex.ru/maps/doc/jsapi/2.1/ref/reference/multiRouter.MultiRouteModel.xml
       */
      var multiRoute = new ymaps.multiRouter.MultiRoute({
          // Описание опорных точек мультимаршрута.
          referencePoints: [
              [43.202767, 76.892698],
              my_end_point,
          ],
          // Параметры маршрутизации.
          params: {
              // Ограничение на максимальное количество маршрутов, возвращаемое маршрутизатором.
              results: 2
          }
      }, {
          // Автоматически устанавливать границы карты так, чтобы маршрут был виден целиком.
          boundsAutoApply: true
      });

      // Создаем кнопки для управления мультимаршрутом.
      var trafficButton = new ymaps.control.Button({
              data: { content: "Учитывать пробки" },
              options: { selectOnClick: true }
          }),
          viaPointButton = new ymaps.control.Button({
              data: { content: "Добавить транзитную точку" },
              options: { selectOnClick: true }
          });

      // Объявляем обработчики для кнопок.
      trafficButton.events.add('select', function () {
          /**
           * Задаем параметры маршрутизации для модели мультимаршрута.
           * @see https://api.yandex.ru/maps/doc/jsapi/2.1/ref/reference/multiRouter.MultiRouteModel.xml#setParams
           */
          multiRoute.model.setParams({ avoidTrafficJams: true }, true);
      });

      trafficButton.events.add('deselect', function () {
          multiRoute.model.setParams({ avoidTrafficJams: false }, true);
      });

      viaPointButton.events.add('select', function () {
          var referencePoints = multiRoute.model.getReferencePoints();
          referencePoints.splice(1, 0, "Алматы, Маркова 47а");
          referencePoints.splice(1, 0, "Алматы, Попова 21");
        
          
          /**
           * Добавляем транзитную точку в модель мультимаршрута.
           * Обратите внимание, что транзитные точки могут находится только
           * между двумя путевыми точками, т.е. не могут быть крайними точками маршрута.
           * @see https://api.yandex.ru/maps/doc/jsapi/2.1/ref/reference/multiRouter.MultiRouteModel.xml#setReferencePoints
           */
          multiRoute.model.setReferencePoints(referencePoints, [1]);
      });

      viaPointButton.events.add('deselect', function () {
          var referencePoints = multiRoute.model.getReferencePoints();
          referencePoints.splice(1, 1);
          multiRoute.model.setReferencePoints(referencePoints, []);
      });

      // Создаем карту с добавленными на нее кнопками.
      var myMap = new ymaps.Map('map', {
          center: [43.22519255, 76.86515808],
          zoom: 7,
          controls: [trafficButton, viaPointButton]
      }, {
          buttonMaxWidth: 300
      });

      // Добавляем мультимаршрут на карту.
      myMap.geoObjects.add(multiRoute);
  }

      ymaps.ready(init);

  },
  
}
</script>

	<style>
	#app{
	height: 100%;
	}
        html, body, #map {
            width: 100%; height: 100%; padding: 0; margin: 0;
        }
    </style>

