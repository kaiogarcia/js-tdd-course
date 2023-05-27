import React from 'react';
import { View, TouchableOpacity, StyleSheet } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';

const Footer = ({ onFavorites }) => {
  return (
    <View style={styles.container}>
      <TouchableOpacity style={styles.favoriteButton} onPress={onFavorites}>
        <FontAwesome name="heart" size={24} color="#040404" />
      </TouchableOpacity>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#F5F5F5',
    padding: 10,
  },
  favoriteButton: {
    marginLeft: 'auto',
  },
});

export default Footer;


import React, { useEffect, useState } from 'react';
import { View, Text, FlatList, ImageBackground, StyleSheet } from 'react-native';
import axios from 'axios';

const UsersScreen = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetchUsers();
  }, []);

  const fetchUsers = async () => {
    try {
      const response = await axios.get('https://api.github.com/search/users?q=rgmellon');
      setUsers(response.data.items);
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <View style={styles.container}>
      <ImageBackground
        source={{ uri: 'https://github.githubassets.com/images/modules/logos_page/Octocat.png' }}
        style={styles.backgroundImage}
      >
        <FlatList
          data={users}
          keyExtractor={(item) => item.id.toString()}
          renderItem={({ item }) => (
            <View style={styles.userContainer}>
              <Text>{item.login}</Text>
              {/* Renderizar outras informações do usuário, se necessário */}
            </View>
          )}
        />
      </ImageBackground>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
  },
  backgroundImage: {
    flex: 1,
    resizeMode: 'cover',
    justifyContent: 'center',
    alignItems: 'center',
  },
  userContainer: {
    backgroundColor: '#7EB6FF',
    padding: 10,
    margin: 10,
    borderRadius: 5,
  },
});

export default UsersScreen;

  
import React, { useEffect, useState } from 'react';
import { View, Text, FlatList, StyleSheet } from 'react-native';
import axios from 'axios';

const RepositoriesScreen = ({ route }) => {
  const [repositories, setRepositories] = useState([]);

  useEffect(() => {
    fetchRepositories();
  }, []);

  const fetchRepositories = async () => {
    try {
      const response = await axios.get(`https://api.github.com/users/${route.params.username}/repos`);
      setRepositories(response.data);
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <View style={styles.container}>
      <FlatList
        data={repositories}
        keyExtractor={(item) => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.repositoryContainer}>
            <Text style={styles.title}>{item.name}</Text>
            <Text style={styles.description}>{item.description}</Text>
            {/* Renderizar outras informações do repositório, se necessário */}
          </View>
        )}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
  },
  repositoryContainer: {
    backgroundColor: '#FDFDFD',
    padding: 10,
    marginVertical: 5,
    marginHorizontal: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#D6D6D6',
  },
  title: {
    color: '#040404',
    fontWeight: 'bold',
  },
  description: {
    color: '#D6D6D6',
  },
});

export default RepositoriesScreen;

      
      
      
      
import React, { useEffect, useState } from 'react';
import { View, Text, FlatList, StyleSheet } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

const FavoritesScreen = () => {
  const [favorites, setFavorites] = useState([]);

  useEffect(() => {
    fetchFavorites();
  }, []);

  const fetchFavorites = async () => {
    try {
      const data = await AsyncStorage.getItem('favorites');
      if (data) {
        setFavorites(JSON.parse(data));
      }
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <View style={styles.container}>
      <FlatList
        data={favorites}
        keyExtractor={(item) => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.favoriteContainer}>
            <Text>{item.login}</Text>
            {/* Renderizar outras informações do usuário favorito, se necessário */}
          </View>
        )}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
  },
  favoriteContainer: {
    backgroundColor: '#FDFDFD',
    padding: 10,
    marginVertical: 5,
    marginHorizontal: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#D6D6D6',
  },
});

export default FavoritesScreen;

      
      
      
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { FontAwesome } from '@expo/vector-icons';

import Header from './src/components/Header';
import Footer from './src/components/Footer';
import UsersScreen from './src/screens/UsersScreen';
import RepositoriesScreen from './src/screens/RepositoriesScreen';
import FavoritesScreen from './src/screens/FavoritesScreen';

const Stack = createStackNavigator();
const Tab = createBottomTabNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Main">
        <Stack.Screen
          name="Main"
          component={MainScreen}
          options={{ headerShown: false }}
        />
        <Stack.Screen
          name="Users"
          component={UsersScreen}
          options={{
            headerTitle: () => <Header />,
            headerStyle: { backgroundColor: '#040404' },
          }}
        />
        <Stack.Screen
          name="Repositories"
          component={RepositoriesScreen}
          options={({ route }) => ({
            headerTitle: route.params.username,
            headerStyle: { backgroundColor: '#040404' },
            headerTintColor: '#FFF',
          })}
        />
        <Stack.Screen
          name="Favorites"
          component={FavoritesScreen}
          options={{
            headerTitle: 'Favorites',
            headerStyle: { backgroundColor: '#040404' },
            headerTintColor: '#FFF',
          }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

const MainScreen = () => {
  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          let iconName;

          if (route.name === 'Users') {
            iconName = focused ? 'users' : 'users';
          } else if (route.name === 'Favorites') {
            iconName = focused ? 'heart' : 'heart-o';
          }

          return <FontAwesome name={iconName} size={size} color={color} />;
        },
      })}
      tabBarOptions={{
        activeTintColor: '#040404',
        inactiveTintColor: 'gray',
        style: { backgroundColor: '#F5F5F5' },
      }}
    >
      <Tab.Screen name="Users" component={UsersScreen} />
      <Tab.Screen name="Favorites" component={FavoritesScreen} />
    </Tab.Navigator>
  );
};

export default App;
