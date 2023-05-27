import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';

const Header = () => {
  return (
    <View style={styles.container}>
      <FontAwesome name="github" color="#040404" size={24} />
      <Text style={styles.title}>GitHub App</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#040404',
    padding: 10,
  },
  title: {
    color: '#FFF',
    fontSize: 18,
    marginLeft: 10,
  },
});

export default Header;

import React from 'react';
import { TouchableOpacity, Text, StyleSheet } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';

const SearchButton = ({ onPress }) => {
  return (
    <TouchableOpacity style={styles.container} onPress={onPress}>
      <FontAwesome name="search" color="#040404" size={20} />
      <Text style={styles.text}>Buscar</Text>
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#F5F5F5',
    padding: 10,
    borderRadius: 5,
  },
  text: {
    marginLeft: 5,
    color: '#040404',
    fontSize: 16,
  },
});

export default SearchButton;

import React from 'react';
import { TouchableOpacity, Text, StyleSheet } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';

const FavoritesButton = ({ onPress }) => {
  return (
    <TouchableOpacity style={styles.container} onPress={onPress}>
      <FontAwesome name="heart" color="#040404" size={20} />
      <Text style={styles.text}>Favoritos</Text>
    </TouchableOpacity>
  );
};

const styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#F5F5F5',
    padding: 10,
    borderRadius: 5,
  },
  text: {
    marginLeft: 5,
    color: '#040404',
    fontSize: 16,
  },
});

export default FavoritesButton;

import React, { useEffect, useState } from 'react';
import { View, Text, StyleSheet, FlatList, ImageBackground } from 'react-native';
import axios from 'axios';

const UserList = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await axios.get('https://api.github.com/search/users?q=rgmellon');
        setUsers(response.data.items);
      } catch (error) {
        console.log(error);
      }
    };

    fetchUsers();
  }, []);

  const renderUserItem = ({ item }) => {
    return (
      <View style={styles.userItem}>
        <ImageBackground
          source={{ uri: item.avatar_url }}
          style={styles.avatar}
          resizeMode="cover"
        >
          <Text style={styles.username}>{item.login}</Text>
        </ImageBackground>
      </View>
    );
  };

  return (
    <View style={styles.container}>
      {users.length === 0 ? (
        <ImageBackground
          source={{ uri: 'https://github.githubassets.com/images/modules/logos_page/Octocat.png' }}
          style={styles.emptyBackground}
          resizeMode="contain"
        >
          <Text style={styles.emptyText}>Está meio vazio por aqui! Busque por um usuário</Text>
        </ImageBackground>
      ) : (
        <FlatList
          data={users}
          renderItem={renderUserItem}
          keyExtractor={(item) => item.id.toString()}
          contentContainerStyle={styles.userList}
        />
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
  },
  emptyBackground: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    paddingHorizontal: 20,
  },
  emptyText: {
    fontSize: 16,
    textAlign: 'center',
    color: '#040404',
  },
  userList: {
    padding: 10,
  },
  userItem: {
    marginBottom: 10,
  },
  avatar: {
    height: 150,
    justifyContent: 'flex-end',
    padding: 10,
  },
  username: {
    fontSize: 16,
    color: '#FFF',
    fontWeight: 'bold',
  },
});

export default UserList;

  
 import React, { useEffect, useState } from 'react';
import { View, Text, StyleSheet, FlatList } from 'react-native';
import axios from 'axios';

const RepositoryList = ({ username }) => {
  const [repositories, setRepositories] = useState([]);

  useEffect(() => {
    const fetchRepositories = async () => {
      try {
        const response = await axios.get(`https://api.github.com/users/${username}/repos`);
        setRepositories(response.data);
      } catch (error) {
        console.log(error);
      }
    };

    fetchRepositories();
  }, [username]);

  const renderRepositoryItem = ({ item }) => {
    return (
      <View style={styles.repositoryItem}>
        <Text style={styles.repositoryName}>{item.name}</Text>
        <Text style={styles.repositoryDescription}>{item.description}</Text>
      </View>
    );
  };

  return (
    <View style={styles.container}>
      <FlatList
        data={repositories}
        renderItem={renderRepositoryItem}
        keyExtractor={(item) => item.id.toString()}
        contentContainerStyle={styles.repositoryList}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
    padding: 10,
  },
  repositoryList: {
    paddingBottom: 10,
  },
  repositoryItem: {
    marginBottom: 10,
    borderWidth: 1,
    borderColor: '#7EB6FF',
    padding: 10,
    borderRadius: 5,
  },
  repositoryName: {
    fontSize: 16,
    color: '#040404',
    fontWeight: 'bold',
  },
  repositoryDescription: {
    fontSize: 14,
    color: '#D6D6D6',
    marginTop: 5,
  },
});

export default RepositoryList;

      
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { FontAwesome } from '@expo/vector-icons';
import Header from './components/Header';
import SearchButton from './components/SearchButton';
import FavoritesButton from './components/FavoritesButton';
import UserList from './components/UserList';
import RepositoryList from './components/RepositoryList';

const Tab = createBottomTabNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Header />
      <Tab.Navigator
        tabBarOptions={{
          style: {
            backgroundColor: '#F5F5F5',
            borderTopColor: '#040404',
            borderTopWidth: 1,
          },
          activeTintColor: '#040404',
          inactiveTintColor: '#D6D6D6',
        }}
      >
        <Tab.Screen
          name="Users"
          component={UserList}
          options={{
            tabBarLabel: 'Usuários',
            tabBarIcon: ({ color, size }) => (
              <FontAwesome name="users" color={color} size={size} />
            ),
            tabBarRightButton: () => <SearchButton onPress={() => {}} />,
          }}
        />
        <Tab.Screen
          name="Favorites"
          component={FavoritesButton}
          options={{
            tabBarLabel: 'Favoritos',
            tabBarIcon: ({ color, size }) => (
              <FontAwesome name="heart" color={color} size={size} />
            ),
            tabBarRightButton: () => <FavoritesButton onPress={() => {}} />,
          }}
        />
      </Tab.Navigator>
    </NavigationContainer>
  );
};

export default App;
