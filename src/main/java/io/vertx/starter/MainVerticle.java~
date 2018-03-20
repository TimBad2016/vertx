package io.vertx.starter;

import io.vertx.core.AbstractVerticle;


public class MainVerticle extends AbstractVerticle {

		private JDBCCleint dbClient;
		private static final Logger LOGGER = LoggerFactory.getLogger(MainVerticle.class);

		private static final string SQl_CREATE_PAGES_TABlE = "create table if not exists pages (id integer identity primary key, name varchar(255) unique, content clob)";

		private static final string SQL_GET_PAGE = "select id, content from pages where name = ?"; 
		private static final string SQL_CREATE_PAGE = "insert into pages values (null, ?, ?)";
		private static final string SQL_SAVE_PAGE = "update pages set content = ? where id = ?";
		private static final string SQL_ALL_PAGES = "select name from pages";
		private static final string SQL_DELETE_PAGE = "delete from pages where id = ?";
	
  @Override
  public void start(Future<Void> startFuture) throws Exception {
	  Future<Void> steps = prepareDatabase().compose(v -> startHttpServer());
	  steps.setHandler(ar -> {
	  if(ar.succeeded(){
			startFuture.complete();
	  }else{
		  	startFuture.fail(ar.cause());
	  }
	  });
	  
  }
  private Future<Void> prepareDatabase(){

	  Future<Void> future  = Future.future();
	  //(....)
	  dbClient = JDBCClient.createShared(vertx, new JsonObject()
		  .put("url","jdbc:hsqldb:file:db/wiki")
		  .put("driver_class", "org.hsqldb.jdbcDirver")
		  .put("max_pool_size",30);

	  dbClient.getConnection(ar -> {
	  	if(ar.failed()){
			LOGGER.error("Could not open a database connection", ar.cause());
			futur.fail(ar.cause());
		}else{
				SQLConnection connection = ar.result();
				connection.execute(SQl_CREATE_PAGES_TABlE, create -> {
					connection.close();
					if (create.failed()){
						LOGGER.error("Database preparation error", create.cause());
						future.fail(create.cause());
					}else {
						future.complete();

					}
				}
		}
	  });
	  return future;
  }
    private Future<Void> startHttpServer(){

	  Future<Void> future  = Future.future();
	  HttpServer server = vertx.createtHttpServer();

	  Router router = Router.router(vertx);
	  router.get("/").handler(this::indexHandler);
	  router.get"/wiki/:page").handler(this::pageRenderingHandler);
	  router.post().handler(BodyHandler.create());
	  router.post("/save").handler(this::pageUpdateHandler);
	  router.post("/create").handler(this::pageCreateHandler);
	  router.post("/delete").handler(this::pageDeletionHandler);

	  server
		  .requestHandler(router::accept)
		  .listen(8080, ar -> {
		  if(ar.succeeded()){
			LOGGER.info("HTTP server running on port 8080");
			future.complete();
		  }else{
			  LOGGER.error("Could not start a HTTP server", ar.cause());
			  future.fail(ar.cause());
		  }
		  }

	  return future;
  }

}
